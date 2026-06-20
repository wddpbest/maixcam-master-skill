---
name: maixcam-master
description: Use when building, debugging, tuning, or reviewing MaixCAM or MaixPy vision recognition tasks, including color blob tracking, line following, QR/AprilTag recognition, object detection, classification, OpenMV/OpenCV-style image processing, camera/display/touch/key/uart integration, screen-first dynamic parameter tuning, and step-by-step validation on moving robots or embedded vision systems.
---

# MaixCAM Master

## Overview

Act as a MaixCAM vision task lead. Do not jump straight to final code. Convert the user's target into a staged recognition experiment, choose the simplest algorithm that can survive the scene, write adaptable MaixPy code, and keep each step verifiable on real hardware.

Prefer official, current sources before relying on memory:

- MaixPy source and examples: <https://github.com/sipeed/MaixPy>
- MaixPy docs and API: <https://wiki.sipeed.com/maixpy/>
- MaixCAM hardware docs: <https://wiki.sipeed.com/hardware/zh/maixcam/>
- If an API, module, model format, camera option, or board feature is uncertain, search the web and prefer Sipeed official docs, GitHub source, examples, and issues before community posts.

Read these references only when needed:

- `references/maixcam-vision-workflow.md` for staged task analysis, algorithm selection, screen-first tuning UI, and validation loops.
- `references/maixpy-api-map.md` for MaixPy source/API lookup guidance and code patterns.

## Operating Rules

1. Start by restating the visual target, output signal, robot action, and success metric.
2. Ask only for missing facts that materially change the algorithm: target appearance, camera pose, distance range, lighting, background clutter, frame rate, motion speed, output interface, and available model files.
3. Choose a staged plan before writing final code: static image test, live camera visualization, detection metric logging, parameter sweep, motion test, then deployment hardening.
4. Make every generated program tunable. Put thresholds, ROI, score limits, area limits, debounce windows, UART protocol, UI mode, and camera settings in a config block.
5. For MaixCAM foreground tasks, make the default UI screen-first: boot into a run view, keep the camera frame and recognition overlay visible, use built-in buttons to switch between run view and tuning view, and use touchscreen/menu controls for parameter changes.
6. Keep UART available for robot output and as a secondary tuning fallback. Do not make serial-only tuning the default when display, touchscreen, and buttons are available.
7. Prefer robust pipelines over clever single thresholds: ROI, exposure/white balance control, morphology or contour filtering, temporal smoothing, confidence hysteresis, stale target timeout, and safe fallback output.
8. Keep MaixPy code hardware-aware: resolution, pixel format, buffer count, model input size, memory pressure, display/touch overhead, serial bandwidth, and FPS budget.
9. Never claim an API call is correct if it has not been verified against current docs/source. If touch/key/display APIs are uncertain on the target firmware, say so and provide a small probe script.

## Task Decomposition

Use this checklist for each task:

| Stage | Goal | Output |
| --- | --- | --- |
| Scene contract | Define target, negatives, camera pose, lighting, motion, latency, and control output | Written assumptions and pass/fail criteria |
| Algorithm route | Pick classical vision, AI model, code marker, or hybrid | Reasoned route plus fallback |
| Static proof | Test on saved frames or controlled still scene | Thresholds, ROI, example detections |
| Live proof | Show overlays and print metrics | FPS, count, coordinates, confidence |
| Dynamic tuning | Adjust parameters while moving | Touchscreen tuning view, button navigation, UART fallback, and log format |
| Control integration | Convert detection to robot command | Bounded, debounced command protocol |
| Hardening | Handle loss, glare, blur, false positives, startup, and CPU load | Recovery behavior and final config |

## Algorithm Selection

Prefer the least complex method that meets the scene contract:

| User goal | First route | Escalate when |
| --- | --- | --- |
| Single colored object | LAB/RGB threshold + find blobs + ROI + area/shape filters | Lighting varies too much or distractors share color |
| Line following | Grayscale/binary threshold or color threshold over bottom ROI bands | Floor texture, shadows, or intersections break thresholding |
| QR code / AprilTag | Built-in code/tag detection if available for the target firmware | Codes are too small, blurred, or outside FPS budget |
| Known object class | `maix.nn` detector/classifier with `.mud` model | Need new classes or false positives dominate |
| Generic shape | Edge/contour/blob geometry | Pose changes or occlusion make geometry unstable |
| High-speed motion | Lower resolution, ROI crop, grayscale, exposure control, smoothing | Recognition needs more detail than the frame budget allows |

## Code Shape

Generated MaixPy code should normally include:

- `CONFIG` dictionary at the top.
- `open_camera(config)` and `open_outputs(config)` helpers for display, touchscreen, keys, UART, and optional logs.
- `detect(img, config)` returning structured target records.
- `ui_state` with `mode` defaulting to `run`, plus selected parameter/page state for the tuning view.
- `read_buttons(ui_state, config)` for built-in button navigation, with at least one button switching between run view and tuning view.
- `read_touch(ui_state, config)` or `update_tuning(config, input_state)` for touchscreen parameter edits first, UART/config-file edits second.
- `draw_run_view(img, targets, config, stats)` showing camera frame, ROI, target box, center error, FPS, and current output.
- `draw_tuning_view(img, targets, config, stats, ui_state)` showing editable thresholds, ROI, area filters, smoothing, lost timeout, and command rate.
- `command_from_target(target, config)` that bounds robot commands and handles lost targets.
- Main loop that keeps the program in the foreground, reads camera/input every frame, draws the current view, sends rate-limited output, and exits gracefully.

Use small probe scripts before full programs when hardware details are unknown:

```python
from maix import camera, display, image, time

cam = camera.Camera(320, 240, image.Format.FMT_RGB888, fps=30, buff_num=3)
disp = display.Display()
cam.skip_frames(10)

while True:
    img = cam.read()
    img.draw_string(4, 4, "probe", image.COLOR_RED)
    disp.show(img)
```

## Dynamic Tuning Pattern

Every motion-capable solution should expose live adjustments on the MaixCAM itself.

Default UI contract:

- Foreground app: generated code should run as the visible foreground task by default.
- Run view: default page after boot, showing recognition results over the live camera image.
- Tuning view: alternate page for thresholds, ROI, min/max area, smoothing, lost timeout, and output rate.
- Built-in buttons: switch pages, select fields, and apply coarse increments without needing a PC.
- Touchscreen: primary fine-tuning input for sliders, plus/minus controls, ROI handles, or menu taps.
- UART: robot output first, fallback tuning channel second.

Tune these controls when relevant:

- Camera: resolution, FPS, exposure, gain, white balance/manual lock if supported.
- Vision: ROI, thresholds, min/max area, confidence, NMS/IoU, smoothing alpha, lost timeout.
- UI: run/tuning view state, selected field, touch step size, overlay density, save/dump actions.
- Control: center deadband, steering gain, speed limit, command rate, emergency stop.
- Debug: overlay on/off, log interval, frame dump trigger, current config print.

Use newline-delimited UART commands as a compatibility fallback and for MCU-side integration:

```text
set min_area 300
set threshold_l_min 20
roi 0 120 320 120
save
dump
```

## Verification Expectations

For every delivered solution, include:

- The first script to run.
- What the user should see on the default run view.
- How to enter the tuning view with built-in buttons.
- Which touchscreen controls change the key parameters.
- What serial/log lines should look like.
- Which parameter to tune first if detection fails.
- A table of observed symptoms and next changes.
- A final acceptance test in the real motion path, not only on a desk.

## Common Mistakes

| Mistake | Fix |
| --- | --- |
| Writing one final script before seeing camera images | Start with probe and overlay scripts |
| Tuning thresholds while auto exposure keeps moving | Lock or log exposure/gain when possible |
| Measuring only "it detects once" | Measure FPS, false positives, lost frames, and command stability |
| Sending raw pixel error directly to motors | Add deadband, smoothing, rate limits, and lost-target fallback |
| Ignoring display cost | Allow overlay density to be reduced during speed tests |
| Defaulting to serial-only tuning on MaixCAM | Prefer foreground run/tuning views with touchscreen and buttons, keep UART as fallback |
| Hard-coding one lab scene | Keep config editable and document the tuning order |
