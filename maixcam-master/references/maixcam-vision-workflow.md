# MaixCAM Vision Workflow

Use this reference when a task needs staged analysis, algorithm choice, validation, or runtime tuning.

## Intake Questions

Ask only the missing questions that change the solution:

| Category | Ask |
| --- | --- |
| Target | What must be recognized, and what must be ignored? |
| Scene | Distance range, camera angle, lens/FOV, lighting, background clutter |
| Motion | Robot speed, target speed, vibration, blur, acceptable latency |
| Output | Display only, UART to MCU, GPIO/PWM, file log, network stream |
| UI | Touchscreen available, built-in button mapping, desired run/tuning pages |
| Accuracy | Required FPS, max false positives, max missed frames, coordinate precision |
| Assets | Existing `.mud` model, labels, sample images/video, MaixPy version |

## Validation Loop

1. Probe camera and display at the intended resolution.
2. Probe touchscreen and built-in buttons, or document the fallback if unavailable.
3. Capture representative frames: bright, dark, target absent, distractor present, motion blur.
4. Build a static detector and print raw metrics: color values, blob area, center, confidence.
5. Add the default run view: ROI, bounding box, center error, threshold values, FPS, and output state.
6. Add the tuning view: editable thresholds, ROI, area filters, smoothing, lost timeout, and send rate.
7. Run live but stationary; tune thresholds and ROI from the touchscreen, with buttons switching views/fields.
8. Run slow motion; add smoothing and lost-target handling.
9. Run target-speed motion; tune command rate, deadband, speed, and fallback.
10. Save final config and document the tuning order.

## Algorithm Routes

### Color Blob

Use when the target color is distinct. Start with a tight ROI and min/max area. Add shape filters only after the color mask is stable. Log the largest blob area and center error.

### Line Following

Use multiple horizontal ROI bands near the lower half of the frame. Compute each band's line center, weight lower bands more, and smooth the steering error. Detect intersections by sudden width/area changes.

### Marker Recognition

Use QR/AprilTag when the scene allows printed markers. Verify marker size in pixels and expected reading distance before promising FPS.

### AI Detection or Classification

Use `maix.nn` when appearance varies too much for classical vision or when the target is semantic. Confirm model path, input size, labels, score threshold, NMS/IoU, and whether the model was trained for the camera viewpoint.

### Hybrid

Use classical vision as a prefilter or ROI generator for AI when full-frame detection is too slow. Use AI confirmation only when a candidate passes cheap filters.

## Screen-First Tuning UI

Default every MaixCAM vision app to a foreground interface unless the user explicitly asks for headless operation.

| View | Purpose | Required content |
| --- | --- | --- |
| Run view | Normal startup page during robot operation | Live image, ROI, target box/center, center error or command, FPS, lost state |
| Tuning view | On-device adjustment while the robot/target moves | Selected parameter, current value, plus/minus or slider controls, save/dump action |

Button behavior should be simple and memorable:

- One button switches between run view and tuning view.
- Next/previous buttons move between parameters or pages.
- OK/confirm applies, saves, or toggles the selected item.
- Long press may reset a parameter or dump config, but never surprise-stop the robot without a visible warning.

Touchscreen behavior should favor fast field tuning: tap controls, drag ROI handles, or use large plus/minus zones. Keep touch handling non-blocking so detection and UART output stay responsive.

## Tuning Order

Tune in this order:

1. Camera pose and focus.
2. Resolution and ROI.
3. Exposure/gain/white balance stability.
4. Thresholds or model score.
5. Area/shape filters.
6. Temporal smoothing and lost timeout.
7. Control deadband/gain/speed.
8. Run/tuning view usability, overlay density, and log rate.

## Symptom Table

| Symptom | Likely cause | Next step |
| --- | --- | --- |
| Target flickers in/out | Threshold too tight, blur, auto exposure drift | Log color/confidence, widen threshold, reduce speed, lock exposure if possible |
| Many false positives | Background shares features | Add ROI, area/shape filters, temporal confirmation, or AI confirmation |
| FPS too low | Resolution/model/display too heavy | Lower resolution, crop ROI, reduce overlay density, use simpler algorithm |
| Button/touch tuning feels laggy | Input handling blocks camera loop or redraw is too heavy | Poll input non-blocking, draw less text, rate-limit UI refresh |
| Robot oscillates | Control gain too high or no smoothing | Add deadband, low-pass center error, limit command rate |
| Works on desk but fails moving | Blur/vibration/lighting changes | Shorten exposure, reduce speed, add motion test frames, retune live |

## Acceptance Test

Define a final test that includes:

- Three lighting conditions or the real competition/field lighting.
- At least ten passes through the motion path.
- Target absent and distractor-present cases.
- Default boot into run view with visible recognition overlay.
- Button switching between run view and tuning view while detection continues.
- Touchscreen adjustment of at least one critical parameter during motion.
- Logged FPS, detection rate, false positives, lost-target events, and command output.
- A saved final config block.
