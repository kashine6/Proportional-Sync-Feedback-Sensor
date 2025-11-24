# PFS (Proportional Feedback Sensor) for MMU

This is a Proportional Feedback Sensor solution designed for MMU systems.

<img src="Assets/8.png" width="70%"/>



## 🧩 What Is a Proportional Feedback Sensor?

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
- **Power input must be 3.3V only.**  **Do NOT use 5V — this may damage your controller board.**

<img src="Assets/7.png" width="70%"/>





## ⚠ ADC Pull-up Considerations

Some boards—such as **MMB, EBB36, or EBB42**—have **built-in pull-up resistors** on all available ADC inputs.

To support both boards with and without pull-up resistors,  this sensor board uses a special design compatible with both cases.



Using a MMU board with ADC pull-ups will cause the sensor's ADC reading to shift upward by approximately **+0.1V** (depending on pull-up resistance).

You can compensate for this in firmware configuration.



**Optional but not needed:**

If your MMU board's ADC pin does not include a pull-up resistor and you want a perfectly accurate voltage reading.
 You may **move the 0-ohm resistor** on the PSF board to an alternate position to eliminate any offset.

<img src="Assets/6.png" width="70%"/>



A list of recommended ADC-capable pins for common MMU boards will be provided below:






## 📦 Bill of Materials (BOM)

| Item                            | Specification                       | Quantity |
| ------------------------------- | ----------------------------------- | -------- |
| **PSF Board**                   | —                                   | 1        |
| **Spring**                      | 0.4 mm × 6 mm × 25 mm, spring steel | 1        |
| **Magnet**                      | D4 mm × 15 mm N35                   | 1        |
| **ECAS04 Bowden connector**     | —                                   | 2        |
| **M2×6 mm self-tapping screw**  |                                     | 2        |
| **M2×10 mm self-tapping screw** |                                     | 2        |

 

A testing version of the kit is now available on AliExpress:







## 🔧 Firmware Support (HappyHare)

At the moment, HappyHare has **not yet fully integrated** support for Proportional Feedback Sensors.

Relevant Pull Request:  
 https://github.com/moggieuk/Happy-Hare/pull/779

To use this feature right now, you must switch to igiannakas’ branch:  
 https://github.com/igiannakas/Happy-Hare/tree/proportional-sync-feedback-control-fixes



