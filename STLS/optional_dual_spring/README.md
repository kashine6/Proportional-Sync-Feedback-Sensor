##  ⚙️ Calibration for dual_spring

**1. Enable detailed logging**

In `mmu_parameters.cfg`, temporarily set:

```
log_level: 4
```

This will let you observe the sensor state in real time.

You need to adjust the configuration in the `mmu_hardware.cfg` file within HappyHare according to the situation.



**2. Determine the correct direction (`sync_feedback_fps_reversed`)**

Move the slider to its left and right limit positions and observe the log output:

- If the front-end trigger reports `tension`, set:

  ```
  sync_feedback_fps_reversed: True
  ```

- If the front-end trigger reports **`compressed`**, set:

  ```
  sync_feedback_fps_reversed: False
  ```






**3. Set the neutral position (`sync_feedback_fps_set_point`)**

```
sync_feedback_fps_set_point: 0.5
```

Notes:

- On boards `without ADC pull-ups`, the idle value should be very close to **0.5**.
- On boards `with pull-ups`, a small offset is normal (typically **0.45–0.55**).
- You may adjust this value slightly depending on whether the neutral reading drifts toward 0 or toward the extremes.
- Whether the value rises or falls depends on the `magnet orientation.



**4. Adjust the usable range (`sync_feedback_fps_range_multiplier`)**

The Hall sensor is non-linear near its extremes, so the design does not use the full ADC range by default.

Increase this multiplier (e.g., **1.1 or 1.2**) so that the left/right extremes map closer to ±1 in the firmware logs.

Example log messages when correctly tuned:

```
STEPPER: MmuSyncFeedbackManager(inactive): Got sync force feedback update. State: tension (-1.0)
STEPPER: MmuSyncFeedbackManager(inactive): Got sync force feedback update. State: compressed (1.0)
```

Default value:

```
sync_feedback_fps_range_multiplier: 1.0
```


