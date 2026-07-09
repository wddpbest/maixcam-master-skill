# MaixPy API Map

Use this reference when writing or checking MaixPy code. Check this file before browsing. Verify live APIs against official docs/source only when this file does not cover the case, target firmware may differ, or device output contradicts these notes.

## Official Sources

- MaixPy repository: <https://github.com/sipeed/MaixPy>
- MaixPy examples: <https://github.com/sipeed/MaixPy/tree/main/examples>
- Vision examples: <https://github.com/sipeed/MaixPy/tree/main/examples/vision>
- MaixPy docs: <https://wiki.sipeed.com/maixpy/>
- MaixPy API index: <https://wiki.sipeed.com/maixpy/api/index.html>
- Camera API: <https://wiki.sipeed.com/maixpy/api/maix/camera.html>
- Display API: <https://wiki.sipeed.com/maixpy/api/maix/display.html>
- Touchscreen API: <https://wiki.sipeed.com/maixpy/api/maix/touchscreen.html>
- Image API: <https://wiki.sipeed.com/maixpy/api/maix/image.html>
- NN API: <https://wiki.sipeed.com/maixpy/api/maix/nn.html>
- UART API: <https://wiki.sipeed.com/maixpy/api/maix/peripheral/uart.html>
- Key API: <https://wiki.sipeed.com/maixpy/api/maix/peripheral/key.html>
- MaixCAM hardware docs: <https://wiki.sipeed.com/hardware/zh/maixcam/>

## Common Imports

```python
from maix import camera, display, image, time
from maix import nn, touchscreen
from maix.peripheral import uart, key
```

Check imports against the current firmware. Some examples use `from maix import uart`; others use `from maix.peripheral import uart` according to API grouping. Touchscreen is documented as `maix.touchscreen`; keys are documented under `maix.peripheral.key`.

## Camera Notes

Camera construction commonly includes width, height, image format, fps, and buffer count. Favor explicit settings in deployable code:

```python
cam = camera.Camera(320, 240, image.Format.FMT_RGB888, fps=30, buff_num=3)
cam.skip_frames(10)
img = cam.read()
```

When chasing latency, consider lower resolution, lower buffer count only after testing, `clear_buff()` before critical reads, and reducing overlay density.

When chasing stability, probe camera getters/setters such as resolution, FPS, exposure, gain, mirror, and flip against the current API docs.

## Display, Touch, and Keys

Use display output for early validation and as the default runtime surface. Make overlay density configurable because drawing and display refresh can dominate runtime.

Firmware compatibility notes from MaixCAM-Pro field logs:

- `time.FPS()` may expose `start()`, `fps()`, and `end()`, but not `tick()`. Use `fps.start()` before the measured block and `fps.fps()`/`fps.end()` after it, or use module-level `time.fps()`.
- Do not assume every named image color constant exists. For example, some builds have `COLOR_GRAY` but not `COLOR_CYAN`. Use `getattr(image, "COLOR_CYAN", image.COLOR_BLUE)` or stick to known basic colors after probing.
- For compact 320x240 tuning UIs, avoid `COLOR_WHITE`/`COLOR_BLACK` text on camera or binary previews when readability matters; prefer green/yellow/blue/red accents and keep labels short.
- Keep touch button labels inside stable boxes. A 7-column bottom grid is too narrow for labels such as `L_MIN`; use shorter labels like `L-`, `L+`, `PIX`, `AREA`, `R-X`, or use fewer columns.
- `maix.image.Image.save(path, quality=95)` can save JPG/PNG images. For offline debugging, create `/root/data/image/` and `/root/data/video/`. For H.264 recording, official docs require `image.Format.FMT_YVU420SP` camera frames and `video.Encoder(type=video.VideoType.VIDEO_H264_CBR)`; write `frame.to_bytes(False)` to a `.h264` file.

Debug overlays should show:

- ROI rectangle.
- Target box and center.
- FPS.
- Current threshold/score.
- Lost-target counter or command output.
- Current UI mode: run view or tuning view.

Use touchscreen and built-in keys for on-device tuning before falling back to UART-only control:

```python
from maix import touchscreen
from maix.peripheral import key

ts = touchscreen.TouchScreen()
keys = key.Key()

if ts.available(0):
    x, y, pressed = ts.read()

key_id, state = keys.read()
if state == key.State.KEY_PRESSED:
    if key_id in (key.Keys.KEY_OK, key.Keys.KEY_OPTION):
        ui_state["mode"] = "tuning" if ui_state["mode"] == "run" else "run"
```

The TouchScreen API returns `[x, y, pressed]` from `read()`, and `available(timeout=0)` can be polled without blocking. The Key API returns a key id and state from `read()`, and supports enums such as `KEY_OK`, `KEY_NEXT`, `KEY_PREV`, `KEY_PRESSED`, and `KEY_LONG_PRESSED`.

Key handling pattern:

- Call `key.rm_default_listener()` before creating/using custom key handling when `KEY_OK` should not exit the app.
- Use keys for mode/page switching first. Prefer touchscreen controls for numeric values.

Touch mapping pattern:

```python
def map_touch_to_image(raw_x, raw_y, cfg):
    raw_w = cfg.get("touch_raw_width", 640)
    raw_h = cfg.get("touch_raw_height", 480)
    if cfg.get("touch_auto_scale", True) and raw_x <= cfg["width"] and raw_y <= cfg["height"]:
        raw_w = cfg["width"]
        raw_h = cfg["height"]
    x = int(raw_x * cfg["width"] / raw_w)
    y = int(raw_y * cfg["height"] / raw_h)
    return [max(0, min(cfg["width"] - 1, x)), max(0, min(cfg["height"] - 1, y))]
```

Keep both raw and mapped coordinates visible during calibration. For 320x240 display code, include `touch_raw_width=640`, `touch_raw_height=480`, and `touch_auto_scale=True` in the config unless the user has measured a different touch coordinate space. If touching the screen does nothing, first inspect the visible raw/mapped coordinates and touch error status before changing hit boxes.

Do not silently swallow touch errors:

```python
try:
    event = ts.read()
except Exception as exc:
    ui_state["last_touch"] = "touch read err:%s" % exc
    return None
```

This makes it clear whether the failure is touch API access, coordinate mapping, or hit testing.

## Threshold Binary Preview Pattern

For RGB888 color tuning, MaixPy thresholds use LAB tuples in the form `[L_min, L_max, A_min, A_max, B_min, B_max]`. L is normally 0..100, A and B are normally -128..127.

Pass thresholds as a list of LAB ranges:

```python
threshold = [20, 90, 20, 127, 0, 127]
thresholds = [threshold]
```

In tuning mode, show the current mask with `image.binary()` before drawing controls:

```python
if ui_state["mode"] == "tuning":
    img.binary([cfg["threshold"]], invert=False, copy=False)
    draw_tuning_controls(img, cfg, ui_state)
```

Keep the tuning mask clean. Draw target boxes, center points, ROI frames, and center lines in the run view, not over the binary preview, unless the user asks for geometry diagnostics.

## Classical Vision Code Pattern

Keep algorithm-specific calls easy to swap. Use a detector function with structured records:

```python
def detect_color(img, cfg):
    roi = cfg["roi"]
    threshold = cfg["threshold"]
    blobs = img.find_blobs(
        [threshold],
        roi=tuple(roi),
        pixels_threshold=cfg["min_pixels"],
        area_threshold=cfg["min_area"],
    )
    if not blobs:
        return []
    best = max(blobs, key=lambda b: b.pixels())
    return [{"cx": best.cx(), "cy": best.cy(), "area": best.area(), "pixels": best.pixels(), "rect": best.rect()}]
```

If a method name or blob property differs on the target firmware, replace it after checking the Image API or running a probe that prints `dir(blob)` or a minimal example.

Cached color-tracking facts:

- `find_blobs` expects a list of thresholds, so pass `[cfg["threshold"]]`, not `cfg["threshold"]`.
- `roi` should be a four-value rectangle `(x, y, w, h)`; keep it clamped inside the image.
- `pixels_threshold` filters by matching pixels, and `area_threshold` filters by blob rectangle area.
- Prefer the blob with the largest `pixels()` for single-target color tracking.
- Useful blob accessors are commonly `x()`, `y()`, `w()`, `h()`, `cx()`, `cy()`, `pixels()`, `area()`, and `rect()`. If a firmware exposes tuple-style blobs instead, use a tiny probe script or compatibility wrapper.

## Key and Touch Parameter Page Pattern

Use physical keys only for page switching unless the user explicitly asks for key-based numeric editing. Default tuning should be touch-first:

```python
from maix.peripheral import key

try:
    key.rm_default_listener()
except Exception:
    pass

keys = key.Key()
key_id, state = keys.read()
if state == key.State.KEY_PRESSED:
    if key_id in (key.Keys.KEY_OK, key.Keys.KEY_OPTION):
        ui_state["mode"] = "params" if ui_state["mode"] == "run" else "run"
```

Default touch tuning layout:

- Draw every tunable parameter as a bottom box with only a short label.
- Show the selected parameter's current value on the left side of the screen, outside the bottom boxes.
- Adapt the number of columns and rows to screen width and parameter count.
- Touch a bottom box to select the parameter.
- Draw large `+` and `-` controls on the right side for the selected parameter.
- Draw a `BIN ON/OFF` toggle at the top-right to switch threshold binary preview.
- Hide touch raw/mapped debug text by default. Keep it behind a config flag such as `show_touch_debug` for calibration.
- Keep key handling out of parameter selection and numeric edits unless requested.

Key names to check first for page switching are `KEY_OK` and `KEY_OPTION`. State names to check first are `KEY_PRESSED` and `KEY_LONG_PRESSED`. Use `getattr` fallbacks when generating portable code because firmware builds can expose a smaller key set.

## AI Code Pattern

Use `maix.nn` when a `.mud` model is available and classical vision is not enough. Keep these parameters configurable:

- model path
- labels
- input width/height/format
- score threshold
- NMS/IoU threshold for detectors
- max result count

Always include a fallback note: if the user lacks a model, use MaixHub or another training/export path, then validate on real frames.

## UART Output and Fallback Tuning

Use UART for robot commands and as a fallback tuning channel. Keep protocols newline-delimited and human-readable during development.

Example command outputs:

```text
target,1,cx,164,cy,118,area,921,error,4
cmd,steer,0.08,speed,0.25
target,0,lost,7
```

Example fallback tuning commands:

```text
set min_area 300
set score 0.55
roi 0 120 320 120
dump
save
```

Do not send unbounded control values. Clamp steering/speed, rate-limit output, and define a lost-target command.
