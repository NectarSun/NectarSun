![Nectarsun Logo][nectarsun-logo]
<br />
<div style="text-align: center;">
<a href="https://itunes.apple.com/app/nectar-sun/id1286550945?mt=8" style="display: inline-block;margin-left: auto;/* margin-right: auto; */background:url(https://linkmaker.itunes.apple.com/assets/shared/badges/en-us/appstore-lrg.svg)    no-repeat;width:135px;height:40px;background-size:contain;margin-right: 50px;"></a>
<a href="https://play.google.com/store/apps/details?id=com.nectarsun.android&pcampaignid=MKT-Other-global-all-co-prtnr-py-PartBadge-Mar2515-1" style="display:inline-block;margin-left: auto;margin-right: auto;background:url(https://play.google.com/intl/en_us/badges/images/generic/en_badge_web_generic.png) no-repeat;width:135px;height:40px;background-size:155px 60px;background-position: -10px -10px;"></a>
<p>
  Download the latest binaries from <a href="https://github.com/NectarSun/nectarsun-update-package/releases/latest">our github page</a>.
</p>
</div>

---
###### 3 October 2018
### Release v109
---
#### TLDR;
Minor update fine-tuning some performance aspects of the Nectarsun: temperature measurement callbacks, 85C issue, fixes the MAX_TEMP_REACHED temperature histeresis, grid energy meter synchronization between boards, and increases the period between the grid and sun relay switching.

Additionally, we added some improvements in our Engineering menu.

More details on each one of these changes below.

<br />
#### BUGS FIXED

###### Main Board
- Fixes the grid meter calculation bug, which caused the grid energy meter to increase even when the 'No Grid' mode is selected. So no more weird metrics in the app. The grid energy meter will only measure the energy consumed from the grid, in all operating modes.
- Fixes the histeresis when water temperature in the boiler reaches the set Max Temperature. Now, when the water temperature reaches the Max Temperature, Nectarsun will wait until the temperature drops by a few degress before switching one of the relays back on (Sun or Grid). This is to prevent the Nectarsun from switching the relays very often around the set temperature.
- Adds a handler for the 85.00C. We ignore the 85.00C when receive, as this is the default temperature in the sensor's ROM. Sometimes due to a jitter in the signal the temperature sensor would return it's default value from the memory. This way we're preventing the Nectarsun from thinking that it reached Max Temperature, when it actually received a false positive signal.

###### Power Board
- Fixes the grid energy meter synchronization between the Main board and the Power board. We discovered a problem that when the Power board unexpectedly restarted, it loaded the last grid energy meter value from its internal memory, and then sent this value to the Main board. Now if such a thing happens, we simply compare the value that was loaded from the memory, with the value that came from the Main board, and we use the bigger one. This way, the grid energy meter will never reset to an old value.

<br />
#### FEATURES ADDED

###### Main Board
- Increased the WDT reset period, as in some cases it caused unexpected resets without any reason.
- Reduces the temperature measurement callback interval from 2s to 0.75s, which means that the temperature sensor measurement will be used less than a second later after it was actually taken. More responsiveness is always better.
- Increases the relay switching times. Relays will now take a longer time to switch from Grid to Sun, but by increasing the delays between switching, we ensure that all the currents and voltages on the relay contacts die down and stabilise. Improves operational reliability.
- Adds a new mode in the Engineering Menu - besides the usual ones: 'Auto', 'Boost', 'No Grid' and 'Away', we added a new one - 'All Off'. It is only accessible through the Engineering Menu, so regular users won't see it. It is for device testing, so that the device does not switch to grid or sun when turned on, therefore preventing from accidentally damaging the equipment that might be connected to the output of the Nectarsun. 

<br />
#### FUTURE UPDATES
- Legionella prevention. Periodically (once a week) heat the boiler to 70C, to prevent Legionella infections.
- Add a menu option to allow the user to disable Legionella prevention.
- ‘No comms’ error. Display an error on the screen if the Main Board did not receive a message from ESP or the Power Board after a certain period of time, as this could mean a partial system failure.
- ‘Resetting device’ screen while ESP restarts after a reset.
- Faster MPPT algorithm.

<br />
<br />
###### 28 June 2018
### Release v108
---
#### TLDR;
This is a minor update without many changes to the operation of the Nectarsun, compared to the last one.

The v108 fixes some small issues that we identified after the last update, as well as a couple of improvements to increase Nectarsun reliability. We improved the night-time and morning/evening 'Sun' state performance, increased the voltage limit, and improved the screen display for people who experience screen flipping from time to time. Also, increases safety in engineering menu.

More details below.

<br />
#### BUGS FIXED

###### Main Board
- Fixed the early morning/night power display problem, when  the device is working in 'Sun' mode. Some people complained that during the night when Nectarsun was running in 'No Grid' mode, the display said that it was heating from 'Sun', even though it was night time. From our side this was not a bug, as the display showed the relay state, which was correct - relays were in 'Sun' mode, but it was confusing for the user. In v107 we tried to fix this by switching off the 'Sun' relay at night, when the voltage drops below 10V, but this introduced unwanted rapid relay switching at early mornings and late evenings. So now, we don't switch off the 'Sun' relay at night, but instead when the generated power drops below 5W, we display 'Idle' on the screen.

###### Power Board
- Fixed the MPPT reset threshold. In previous versions our MPPT algorithm would start searching for the MPP from the beginning if the PV power was lesser than 50W and AC PWM duty cycle was greater than 50%. This introduced some unstabilities with low PV power. With v108 we changed this condition from checking for power and PWM duty cycle conditions, to only checking the PV current. Now, the MPPT starts tracking when the PV current becomes greater than 75mA, otherwise, the PWM duty cycle is kept at 10%. This prevents the Nectarsun from interpreting electrical noise in the measurement circuitry as PV power.

<br />
#### FEATURES ADDED

###### Main Board
- The screen on the Nectarsun now reinintialises every 10 minutes. This helps in cases where there is a lot of electrical noise in the environment that can affect the screen display. Also, if the screen was disconnected from the Nectarsun, it will reinintialise in 10 minutes after connecting it back, and the user will not have to restart the Nectarsun anymore.

###### Power Board
- The input voltage limit has been raised to 385V to accommodate the new high-voltage PV panels.
- When the user enters the engineering menu, Nectarsun does not automatically switch the relays. Now the user has to manually go to the 'Select Mode' menu option where after the selection has been made, the Nectarsun switches relays. This has been implemented to protect the hardware (e.g. metering hardware) that might be connected to the Nectarsun output while it is in the engineering menu.

<br />
#### FUTURE UPDATES
- Legionella prevention. Periodically (once a week) heat the boiler to 70C, to prevent Legionella infections.
- Add a menu option to allow the user to disable Legionella prevention.
- ‘No comms’ error. Display an error on the screen if the Main Board did not receive a message from ESP or the Power Board after a certain period of time, as this could mean a partial system failure.
- ‘Resetting device’ screen while ESP restarts after a reset.
- Faster MPPT algorithm.

<br />
<br />
###### 19 June 2018
### Release v107
---
#### TLDR;
The v107 fixes the re-calibration issue (0W in ‘No Grid’ mode), improves communications reliability between the Wifi module and 2 processors, validates the outgoing data to fix the statistics issue in the app, sends calibration data and firmware version to the server for better monitoring.

Also, firmware version of each processor and the Wifi module are now displayed on the screen while Nectarsun is loading to prevent from shipping devices with old firmware or without firmware at all.

More details below.

<br />
#### BUGS FIXED

###### Communications
- Changed the values that represent ‘true’ and ‘false’ from 0x01 and 0x00, to 0xAC and 0xFA respectively. It increases message reliability, and reduces the chances of interpreting the wrong value when the message becomes corrupt.

###### ESP (Wifi module)
- Fixes the HTTPS client library. Does not use the readStringUntil(‘\n’) function to read the response from server, instead uses the response length to read the correct number of chars from server. Previously, this issue caused an error while trying to configure the device with the app.
- Checks outgoing data and does not send a message to the server, if all is zero. Fixes the large ‘Sun kWh’ stats in the app.
- Adds a check when loading data from flash, if the correct value for configuration is not found, we stop reading data from memory and fallback to default settings. Previously ESP would hang and reset while trying to read the URL from flash.
- Reduces SNTP call timeout from 20s to 5s. Increases device responsiveness in case there is a problem with the SNTP server.

###### Main Board
- Turns off the Sun relay at night. Previously it showed ‘Heating from Sun’ on the device screen at night. Now, the device shows ‘Idle’ at night, and when the PV voltage drops below 10V (basically when it becomes dark, or PV is disconnected).
- Fixes the ‘Calibrate’ screen bug. The device calibrated when either ‘Yes’ or ‘No’ was selected. Now it only happens when ‘Yes’ is selected. Prevents from accidental re-calibration.

###### Power Board
- Adds valid data checks for calibration data. When calibrating the device, it checks if the calibration data is within the expected range (voltage value:0.001000-0.002000 and current value: 1.800000-2.200000). If values are within the range, Nectarsun saves the new values in the internal memory. If the values are bad (outside the correct range) they values are ignored, and if the device was previously correctly calibrated, values are loaded from memory. If device was not calibrated previously, it displays the ‘Not calibrated’ message, and raises the CALIBRATION_ERROR flag (Error 232). This prevents from random calibration which renders the Nectarsun useless when heating from sun. Fixes the issues that multiple users faced, when calibration was done wrong, or when Nectarsun improperly calibrated itself.

<br />
#### FEATURES ADDED

###### Communications
- Added checksums to each message to increase message reliability.
- Expanded the comms protocol to include firmware version for each processor.

###### ESP (Wifi module)
- Sends ESP firmware version to the Main Board.
- Sends Main Board firmware version, as well as calibration data to the server.
- Adds support for legacy comms where only the ESP module receives an over-the-air update.

###### Main Board
- Displays non-critical errors on the ‘Active Status’ screen in the bottom right corner. If a Power Board error occurs (over-current, over-voltage, overheat, or similar) that prevents the Power Board from functioning normally, a warning sign (triangle with an exclamation sign) is displayed in the bottom right corner with the error number underneath.  - This will inform the user if the device is heating from ‘Sun’, ‘Idle’, or in safe mode (waiting for Power Board to recover). This does not have any effect if the device is in ‘Grid’ mode.
- Updates the loading screen. Device loads while valid messages are received from the ESP module and the Power Board. Firmware versions of each processor are displayed on the loading screen to increase quality and prevent from shipping old firmware or modules without firmware. Installed firmware now displayed in the engineering menu as well.
- Both relays switch off while the device is calibrating.
- Increases watchdog timer reset period.

###### Power Board
- Increases current limit from 9A to 10A. Allows users with with high-amperage PV panels to use the Nectarsun.
- Increases watchdog timer reset period.

<br />
#### FUTURE UPDATES
- Legionella prevention. Periodically (once a week) heat the boiler to 70C, to prevent Legionella infections.
- Add a menu option to allow the user to disable Legionella prevention.
- ‘No comms’ error. Display an error on the screen if the Main Board did not receive a message from ESP or the Power Board after a certain period of time, as this could mean a partial system failure.
- ‘Resetting device’ screen while ESP restarts after a reset.
- Periodically reinitialise the screen to prevent it from flipping upside-down, etc.

[nectarsun-logo]: https://github.com/NectarSun/nectarsun.github.io/raw/master/img/logo.png "Top cover screws"