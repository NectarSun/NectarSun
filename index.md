<div style="text-align: center;">
<a href="http://nectarsun.com">
  <img src="http://nectarsun.com/img/nectar-logo-3.svg" alt="Nectarsun" style="margin-bottom: 40px; margin-top: 10px;">
</a>
</div>

<div style="text-align: center;">
<a href="https://itunes.apple.com/app/nectar-sun/id1286550945?mt=8" style="display: inline-block;margin-left: auto;/* margin-right: auto; */background:url(https://linkmaker.itunes.apple.com/assets/shared/badges/en-us/appstore-lrg.svg)    no-repeat;width:135px;height:40px;background-size:contain;margin-right: 50px;"></a>
<a href="https://play.google.com/store/apps/details?id=com.nectarsun.android&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1" style="display:inline-block;margin-left: auto;margin-right: auto;background:url(https://play.google.com/intl/en_us/badges/images/generic/en_badge_web_generic.png) no-repeat;width:135px;height:40px;background-size:155px 60px;background-position: -10px -10px;"></a>
</div>

---
###### 19 June 2018
### Release v107
---
#### TLDR;
The v107 fixes the re-calibration issue (0W in ‘No Grid’ mode), improves communications reliability between the Wifi module and 2 processors, validates the outgoing data to fix the statistics issue in the app, sends calibration data and firmware version to the server for better monitoring.

Also, firmware version of each processor and the Wifi module are now displayed on the screen while Nectarsun is loading to prevent from shipping devices with old firmware or without firmware at all.

More details below.


---
#### BUGS FIXED

##### Communications
- Changed the values that represent ‘true’ and ‘false’ from 0x01 and 0x00, to 0xAC and 0xFA respectively. It increases message reliability, and reduces the chances of interpreting the wrong value when the message becomes corrupt.

##### ESP (Wifi module)
- Fixes the HTTPS client library. Does not use the readStringUntil(‘\n’) function to read the response from server, instead uses the response length to read the correct number of chars from server. Previously, this issue caused an error while trying to configure the device with the app.
- Checks outgoing data and does not send a message to the server, if all is zero. Fixes the large ‘Sun kWh’ stats in the app.
- Adds a check when loading data from flash, if the correct value for configuration is not found, we stop reading data from memory and fallback to default settings. Previously ESP would hang and reset while trying to read the URL from flash.
- Reduces SNTP call timeout from 20s to 5s. Increases device responsiveness in case there is a problem with the SNTP server.

##### Main Board
- Turns off the Sun relay at night. Previously it showed ‘Heating from Sun’ on the device screen at night. Now, the device shows ‘Idle’ at night, and when the PV voltage drops below 10V (basically when it becomes dark, or PV is disconnected).
- Fixes the ‘Calibrate’ screen bug. The device calibrated when either ‘Yes’ or ‘No’ was selected. Now it only happens when ‘Yes’ is selected. Prevents from accidental re-calibration.

##### Power Board
- Adds valid data checks for calibration data. When calibrating the device, it checks if the calibration data is within the expected range (voltage value:0.001000-0.002000 and current value: 1.800000-2.200000). If values are within the range, Nectarsun saves the new values in the internal memory. If the values are bad (outside the correct range) they values are ignored, and if the device was previously correctly calibrated, values are loaded from memory. If device was not calibrated previously, it displays the ‘Not calibrated’ message, and raises the CALIBRATION_ERROR flag (Error 232). This prevents from random calibration which renders the Nectarsun useless when heating from sun. Fixes the issues that multiple users faced, when calibration was done wrong, or when Nectarsun improperly calibrated itself.


---
#### FEATURES ADDED

##### Communications

- Added checksums to each message to increase message reliability.
- Expanded the comms protocol to include firmware version for each processor.

##### ESP (Wifi module)
- Sends ESP firmware version to the Main Board.
- Sends Main Board firmware version, as well as calibration data to the server.
- Adds support for legacy comms where only the ESP module receives an over-the-air update.

##### Main Board
- Displays non-critical errors on the ‘Active Status’ screen in the bottom right corner. If a Power Board error occurs (over-current, over-voltage, overheat, or similar) that prevents the Power Board from functioning normally, a warning sign (triangle with an exclamation sign) is displayed in the bottom right corner with the error number underneath.  - This will inform the user if the device is heating from ‘Sun’, ‘Idle’, or in safe mode (waiting for Power Board to recover). This does not have any effect if the device is in ‘Grid’ mode.
- Updates the loading screen. Device loads while valid messages are received from the ESP module and the Power Board. Firmware versions of each processor are displayed on the loading screen to increase quality and prevent from shipping old firmware or modules without firmware. Installed firmware now displayed in the engineering menu as well.
- Both relays switch off while the device is calibrating.
- Increases watchdog timer reset period.

##### Power Board
- Increases current limit from 9A to 10A. Allows users with with high-amperage PV panels to use the Nectarsun.
- Increases watchdog timer reset period.


---
#### FUTURE UPDATES

- Legionella prevention. Periodically (once a week) heat the boiler to 70C, to prevent Legionella infections.
- Add a menu option to allow the user to disable Legionella prevention.
- ‘No comms’ error. Display an error on the screen if the Main Board did not receive a message from ESP or the Power Board after a certain period of time, as this could mean a partial system failure.
- ‘Resetting device’ screen while ESP restarts after a reset.
- Periodically reinitialise the screen to prevent it from flipping upside-down, etc.
