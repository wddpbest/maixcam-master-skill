# MaixPy API Map

Use this reference when writing or checking MaixPy code. Verify live APIs against official docs/source when uncertain.

## Official Sources

- MaixPy repository: <https://github.com/sipeed/MaixPy>
- MaixPy examples: <https://github.com/sipeed/MaixPy/tree/main/examples>
- Vision examples: <https://github.com/sipeed/MaixPy/tree/main/examples/vision>
- MaixPy docs: <https://wiki.sipeed.com/maixpy/>
- MaixPy API index: <https://wiki.sipeed.com/maixpy/api/index.html>
- Camera API: <https://wiki.sipeed.com/maixpy/api/maix/camera.html>
- Display API: <https://wiki.sipeed.com/maixpy/api/maix/display.html>
- Image API: <https://wiki.sipeed.com/maixpy/api/maix/image.html>
- NN API: <https://wiki.sipeed.com/maixpy/api/maix/nn.html>
- UART API: <https://wiki.sipeed.com/maixpy/api/maix/peripheral/uart.html>
- Key API: <https://wiki.sipeed.com/maixpy/api/maix/peripheral/key.html>
- MaixCAM hardware docs: <https://wiki.sipeed.com/hardware/zh/maixcam/>

## Common Imports

```python
from maix import camera, display, image, time
from maix import nn
from maix.peripheral import uart
```

Check imports against the current firmware. Some examples use `from maix import uart`; others use `from maix.peripheral import uart` according to API grouping.

## Camera Notes

Camera construction commonly includes width, height, image format, fps, and buffer count. Favor explicit settings in deployable code:

```python
cam = camera.Camera(320, 240, image.Format.FMT_RGB888, fps=30, buff_num=3)
cam.skip_frames(10)
img = cam.read()
```

When chasing latency, consider lower resolution, lower buffer count only after testing, `clear_buff()` before critical reads, and turning off display overlays.

When chasing stability, probe camera getters/setters such as resolution, FPS, exposure, gain, mirror, and flip against the current API docs.

## Display and Debug

Use display output for early validation. Make it configurable because drawing and display refresh can dominate runtime.

Debug overlays should show:

- ROI rectangle.
- Target box and center.
- FPS.
- Current threshold/score.
- Lost-target counter or command output.

## Classical Vision Code Pattern

Keep algorithm-specific calls easy to swap. Use a detector function with structured records:

```python
def detect_color(img, cfg):
    roi = cfg["roi"]
    thresholds = cfg["thresholds"]
    blobs = img.find_blobs(thresholds, roi=roi, pixels_threshold=cfg["min_pixels"], area_threshold=cfg["min_area"])
    if not blobs:
        return []
    best = max(blobs, key=lambda b: b.pixels())
    return [{"cx": best.cx(), "cy": best.cy(), "area": best.area(), "pixels": best.pixels(), "rect": best.rect()}]
```

If a method name or blob property differs on the target firmware, replace it after checking the Image API or running a probe that prints `dir(blob)` or a minimal example.

## AI Code Pattern

Use `maix.nn` when a `.mud` model is available and classical vision is not enough. Keep these parameters configurable:

- model path
- labels
- input width/height/format
- score threshold
- NMS/IoU threshold for detectors
- max result count

Always include a fallback note: if the user lacks a model, use MaixHub or another training/export path, then validate on real frames.

## UART Tuning and Robot Output

Use UART both for robot commands and runtime tuning when possible. Keep protocols newline-delimited and human-readable during development.

Example command outputs:

```text
target,1,cx,164,cy,118,area,921,error,4
cmd,steer,0.08,speed,0.25
target,0,lost,7
```

Example tuning commands:

```text
set min_area 300
set score 0.55
roi 0 120 320 120
dump
save
```

Do not send unbounded control values. Clamp steering/speed, rate-limit output, and define a lost-target command.

