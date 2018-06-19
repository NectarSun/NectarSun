Here we will post detailed updates about our latest firmware releases.

### Release v107
---
### TLDR;

The v107 fixes the re-calibration issue (0W in ‘No Grid’ mode), improves communications reliability between the Wifi module and 2 processors, validates the outgoing data to fix the statistics issue in the app, sends calibration data and firmware version to the server for better monitoring.

Also, firmware version of each processor and the Wifi module are now displayed on the screen while Nectarsun is loading to prevent from shipping devices with old firmware or without firmware at all.

More details below.

### BUGS FIXED

#### Communications

- Changed the values that represent ‘true’ and ‘false’ from 0x01 and 0x00, to 0xAC and 0xFA respectively. It increases message reliability, and reduces the chances of interpreting the wrong value when the message becomes corrupt.

#### ESP (Wifi module)

- Fixes the HTTPS client library. Does not use the readStringUntil(‘\n’) function to read the response from server, instead uses the response length to read the correct number of chars from server. Previously, this issue caused an error while trying to configure the device with the app.

- Checks outgoing data and does not send a message to the server, if all is zero. Fixes the large ‘Sun kWh’ stats in the app.

- Adds a check when loading data from flash, if the correct value for configuration is not found, we stop reading data from memory and fallback to default settings. Previously ESP would hang and reset while trying to read the URL from flash.

- Reduces SNTP call timeout from 20s to 5s. Increases device responsiveness in case there is a problem with the SNTP server.

#### Main Board

- Turns off the Sun relay at night. Previously it showed ‘Heating from Sun’ on the device screen at night. Now, the device shows ‘Idle’ at night, and when the PV voltage drops below 10V (basically when it becomes dark, or PV is disconnected).

- Fixes the ‘Calibrate’ screen bug. The device calibrated when either ‘Yes’ or ‘No’ was selected. Now it only happens when ‘Yes’ is selected. Prevents from accidental re-calibration.

#### Power Board

- Adds valid data checks for calibration data. When calibrating the device, it checks if the calibration data is within the expected range (voltage value:0.001000-0.002000 and current value: 1.800000-2.200000). If values are within the range, Nectarsun saves the new values in the internal memory. If the values are bad (outside the correct range) they values are ignored, and if the device was previously correctly calibrated, values are loaded from memory. If device was not calibrated previously, it displays the ‘Not calibrated’ message, and raises the CALIBRATION_ERROR flag (Error 232). This prevents from random calibration which renders the Nectarsun useless when heating from sun. Fixes the issues that multiple users faced, when calibration was done wrong, or when Nectarsun improperly calibrated itself.

Markdown is a lightweight and easy-to-use syntax for styling your writing. It includes conventions for

```markdown
Syntax highlighted code block

# Header 1
## Header 2
### Header 3

- Bulleted
- List

1. Numbered
2. List

**Bold** and _Italic_ and `Code` text

[Link](url) and ![Image](src)
```

For more details see [GitHub Flavored Markdown](https://guides.github.com/features/mastering-markdown/).

### Jekyll Themes

Your Pages site will use the layout and styles from the Jekyll theme you have selected in your [repository settings](https://github.com/NectarSun/NectarSun/settings). The name of this theme is saved in the Jekyll `_config.yml` configuration file.

### Support or Contact

Having trouble with Pages? Check out our [documentation](https://help.github.com/categories/github-pages-basics/) or [contact support](https://github.com/contact) and we’ll help you sort it out.
