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
| Accuracy | Required FPS, max false positives, max missed frames, coordinate precision |
| Assets | Existing `.mud` model, labels, sample images/video, MaixPy version |

## Validation Loop

1. Probe camera and display at the intended resolution.
2. Capture representative frames: bright, dark, target absent, distractor present, motion blur.
3. Build a static detector and print raw metrics: color values, blob area, center, confidence.
4. Add overlay: ROI, bounding box, center error, threshold values, FPS.
5. Run live but stationary; tune thresholds and ROI.
6. Run slow motion; add smoothing and lost-target handling.
7. Run target-speed motion; tune command rate, deadband, speed, and fallback.
8. Save final config and document the tuning order.

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

## Tuning Order

Tune in this order:

1. Camera pose and focus.
2. Resolution and ROI.
3. Exposure/gain/white balance stability.
4. Thresholds or model score.
5. Area/shape filters.
6. Temporal smoothing and lost timeout.
7. Control deadband/gain/speed.
8. Debug overlay/log rate.

## Symptom Table

| Symptom | Likely cause | Next step |
| --- | --- | --- |
| Target flickers in/out | Threshold too tight, blur, auto exposure drift | Log color/confidence, widen threshold, reduce speed, lock exposure if possible |
| Many false positives | Background shares features | Add ROI, area/shape filters, temporal confirmation, or AI confirmation |
| FPS too low | Resolution/model/display too heavy | Lower resolution, crop ROI, disable overlay, use simpler algorithm |
| Robot oscillates | Control gain too high or no smoothing | Add deadband, low-pass center error, limit command rate |
| Works on desk but fails moving | Blur/vibration/lighting changes | Shorten exposure, reduce speed, add motion test frames, retune live |

## Acceptance Test

Define a final test that includes:

- Three lighting conditions or the real competition/field lighting.
- At least ten passes through the motion path.
- Target absent and distractor-present cases.
- Logged FPS, detection rate, false positives, lost-target events, and command output.
- A saved final config block.

