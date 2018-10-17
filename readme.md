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