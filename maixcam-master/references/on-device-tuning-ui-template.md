# On-Device Tuning UI Template

Use this contract when a MaixCAM task needs the same compact RUN/TUNE screen while the tunable values change.

## Fixed UI Contract

- Logical canvas: `320x240`; display it with `FIT_CONTAIN`.
- Default mode: `run`; physical `OK` or `OPTION` toggles `run` and `tune`.
- Tuning view: the camera or binary image remains the background.
- Top bar: passive status only. Do not put numeric touch controls in the top bar.
- Large control rectangles are defined once and reused for drawing and hit testing.
- Bottom parameter rectangles select a field; right-side `+` and `-` edit the selected field.
- A tap is emitted on release, after a press has been recorded. Empty-space touches do nothing.
- Map raw touch coordinates back to the logical image with
  `image.resize_map_pos_reverse(..., image.Fit.FIT_CONTAIN, x, y)` when a display object is available.

The current compact layout reserves these roles:

```text
top bar:        y=0..22, passive
current value:  x=4..84,   y=28..72
STEP:           x=92..144, y=28..72
BIN/RGB:        x=152..202,y=28..72
AUTO:           x=210..262,y=28..72
SAVE/TUNE:      x=270..316,y=28..72
plus/minus:     x=252..316,y=82..160
parameters:     bottom grid, y=170..234
```

Keep at least `2 * touch_slop` pixels between neighboring hit rectangles. Do not create a second hidden hit-zone layout that can drift away from the drawn rectangles.

## Parameter Slots

The UI must consume a path-based parameter table rather than indexing `threshold` directly:

```python
TUNABLE_PARAMS = (
    # label, path, default_step, low, high, paired_bound_path
    ("L MIN", ("threshold", 0), 1, 0, 100, ("threshold", 1)),
    ("L MAX", ("threshold", 1), 1, 0, 100, ("threshold", 0)),
)
```

Use shared helpers for every UI operation:

```python
def get_param_value(cfg, path):
    value = cfg
    for key in path:
        value = value[key]
    return value

def set_param_value(cfg, path, value):
    target = cfg
    for key in path[:-1]:
        target = target[key]
    target[path[-1]] = value
```

To port the UI to another task, change only:

1. The task values in `CONFIG`.
2. The entries in `TUNABLE_PARAMS`.
3. The task-specific normalizer, detector, and optional auto-learning function.

Keep the layout constants, `map_touch`, `touch_hit`, release handling, field selection, `+/-` editing, and serialization unchanged. A non-threshold variable can use a one-element path, for example `("min_area",)`, or a nested path such as `("detector", "score")`.

Save both the task's legacy top-level values, when needed by offline tools, and a generic `tunable` map keyed by dotted paths. This lets the same UI restore parameters for color, area, ROI, score, smoothing, or command-rate tasks.

## FPS Contract

`time.FPS()` measures the Python loop. It must not be displayed as camera FPS by itself because camera buffers can be read faster than the sensor produces new frames.

Use the hardware camera rate and the measured loop rate:

```python
camera_rate = float(cam.fps())
loop_rate = fps_value(loop_fps)
effective_rate = min(round(camera_rate), round(loop_rate))
```

Display `effective_rate` as `FPS`. Log the separate values during bring-up when diagnosing performance:

```text
camera_fps,30.0
loop_fps,400
effective_fps,30
```

If `cam.fps()` is unavailable on an older firmware, fall back to the configured camera rate and state that the value is a configured limit, not a sensor measurement.
