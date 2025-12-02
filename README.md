# PSF (Proportional Sync Feedback) Sensor for MMU

This is a Mini Proportional Sync Feedback Sensor solution designed for MMU systems.



**Note:**

Currently shifted to the default single-spring version.
It provides constant compression, which reduces the apparent load on the extruder stepper and helps overcome Bowden friction.

The STLS folder still includes the dual-spring version.



> ⚠️**This project is currently in the testing stage. It will be open-sourced after testing.**

<img src="Assets/11.png" width="70%"/>





## 🧩 What Is a Proportional Sync Feedback Sensor?

A *Proportional Feedback Sensor* is a more advanced type of feedback sensor solution.
Compared to other feedback solutions, it:

- **Outputs a signal ranging from 1.0 (maximum compression) to -1.0 (maximum tension)**
- **0.0 represents the neutral position**

With this signal, the firmware can determine the exact current position of the feedback slider and adjust it toward neutral at any moment.

It can also function as a simplified **clog and tangle detection mechanism without using an encoder**.




## 🛠 Hardware Design

This design does **not** use a dedicated standalone mainboard.
Instead, it leverages the **ADC input** already available on existing MMU boards.


- **You must connect the signal output to a pin that supports ADC input.**
- **Power input must be `3.3V` only.**  ⚠️⚠️**Do NOT use `5V` — this may damage your controller board.**⚠️⚠️

<img src="Assets/7.png" width="70%"/>





## ⚠ ADC Pull-up Considerations

Some boards—such as **MMB, EBB36, or EBB42**—have **built-in pull-up resistors** on all available ADC inputs.

To support both boards with and without pull-up resistors,  this sensor board uses a special design compatible with both cases.



Using an MMU board with ADC pull-ups will cause the sensor's ADC reading to shift upward by approximately +0.1V (not constant; this has been minimized using the amplifier). 
You can adjust the neutral position value in the configuration to fix this issue.



## 📍 Recommended ADC Pins
A list of recommended ADC-capable pins for common MMU boards will be provided below:
<img src="Assets/9.png" width="85%"/>





## 📦 Bill of Materials (BOM)

| Item                            | Specification                                                | Quantity |
| ------------------------------- | ------------------------------------------------------------ | -------- |
| **PSF Board**                   | —                                                            | 1        |
| **Spring**                      | 0.4 mm × 6 mm × 25 mm, spring steel                          | 1        |
| **Magnet**                      | D4 mm × 15 mm N35                                            | 1        |
| **ECAS04 Bowden connector**     | —                                                            | 2        |
| **ECAS_Clip**                   | Please use the STL file for printing to ensure consistent thickness | 2        |
| **M2×6 mm SHCS screw**          |                                                              | 4        |
| **5V-to-3.3V Step-Down Module** | optional: Use when a 3.3V power supply is not available on your mmu board. | 1        |



**A testing version of the kit is now available on AliExpress:**  

[Vano3dla aliexpress](https://www.aliexpress.com/item/1005010470743517.html)




## 🔧 Firmware Support (HappyHare)

At the moment, HappyHare has **not yet fully integrated** support for Proportional Feedback Sensors.

Relevant Pull Request:  
 https://github.com/moggieuk/Happy-Hare/pull/779

To use this feature right now, you must switch to igiannakas’ branch:  
 https://github.com/igiannakas/Happy-Hare/tree/proportional-sync-feedback-control-fixes



##  ⚙️ Calibration

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

<img src="Assets/12.png" width="70%"/>





**3. Set the neutral position (`sync_feedback_fps_set_point`)**

```
sync_feedback_fps_set_point: 0.5
```

Notes:

- You can remove the PCB and adjust sync_feedback_fps_set_point so that the debug output value is close to 0 when there is no magnetic field.

- On boards `without ADC pull-ups`, the idle value should be **0.5**.
- On boards `with pull-ups`, a small offset is normal (typically **0.45–0.55**, my MMB is 0.48/0.52).
- Whether the value rises or falls depends on the `magnet orientation.

``` 
STEPPER: MmuSyncFeedbackManager(active): Got sync force feedback update. State: neutral (-0.0034094111017188293)
```





**4. Adjust the usable range (`sync_feedback_fps_range_multiplier`)**

You can set it to 1.0 or 1.1. I recommend 1.1, which will make the left and right limits reach ±1.

Example log messages when correctly tuned:

```
STEPPER: MmuSyncFeedbackManager(inactive): Got sync force feedback update. State: tension (-1.0)
STEPPER: MmuSyncFeedbackManager(inactive): Got sync force feedback update. State: compressed (1.0)
```

Default value:

```
sync_feedback_fps_range_multiplier: 1.0
```



**5. Modify the contents of `mmu_parameters.cfg`.**

``` 
sync_feedback_enabled: 1
sync_feedback_proportional_sensor: 1
sync_feedback_buffer_range: 9.3     # PSF single-spring version
sync_feedback_buffer_maxrange: 9.3

# option
sync_endguard_enabled: 1
sync_endguard_band: 0.80
sync_endguard_distance_mm: 6.0
```




## 🙏 References & Acknowledgements

- The CAD design is based on modifications of [Tshine's Filament Sync Sensor](https://makerworld.com/en/models/507573).  
- The Proportional Sync Feedback concept is inspired by [OpenAMS FPS](https://github.com/OpenAMSOrg/filament-buffer).  
- Thanks to moggieuk, igiannakas, and KnightRadiant for their excellent code contributions related to Proportional Sync Feedback.
