# Home HASS project
This is home automation for heating control and catching various home alerts using Home Assistant and some additional components.

## Purpose
I have just few main goals to reach initially:
 - Receive alerts from smoke sensors and water flood sensors and send warning immediately
 - Replace old thermostatic radiator valves (TRV) with connected ones
 - Control heating remotely
 - Do not rely on cloud controlled device and keep everything on local network

And as main goals are fulfilled few more goals appears :wink:
 - Handle easily open / close window events and get adequate heating reaction
 - Handle manual intervention on TRVs somehow logically
 - Do not hardcode heating schedules to config files but provide UI
 - Do not send data to China or anywhere else if it can be avoided

## Overview
Whole solution is using quite common components.
On software side:
 - Home Assistant (https://www.home-assistant.io/)
 -- many plugins to Home Assistant via HACS (https://github.com/hacs)
 - AppDaemon (https://github.com/home-assistant/appdaemon)
 - Zigbee2MQTT (https://www.zigbee2mqtt.io/)
 - Schedy for HA (https://github.com/efficiosoft/hass-apps)
 - CustomUI (https://github.com/andrey-git/home-assistant-custom-ui)

On HW side:

 - Xiomi gateway (ZigBee hub) - *no longer acting as a hub*
 - TI C2531 (USB stick for Zigbee network) + RPI Zero
 - TI C1352R-2 (USB dev board for Zibee netwrok) - *to replace the C2531 soon*
 - various Xiaomi Aqara detectors (smoke sensors, flood sensors, door contacts, thermometers)
 - Z-Wave Me UZB (USB stick for Z-Wave network)
 - EUROtronic Spirit Z-Wave (TRVs)
 - few more Z-Wave devices to extend coverage

Current status covered by this repo is  that all the goals are working.
Once any alert is triggered by smoke or flood sensor alerts are sent via Pushbullet.
All TRVs are connected and controlled by Home Assistant in cooperation with Schedy. All settings for temperature values and intervals are adjustable via Home Assistant GUI.

## Heating
This is the complex part of the whole solution. So few remarks how it works and what are limitations (mostly intended)

Everything related to schedules is dynamically set in UI. Schedy is watching defined HASS entities and in case of a change of any entity it performs re-evaluation of the schedule. To handle the regular time based schedules there is na artificial sensor each minute changing itself to number of minutes since midnight. This way each minute there is a change and Schedy reacts.

It is possible to schedule two intervals of high temperature per day and room. For my use cases it is enough. Different setting can be specified for working and non-working days. Working days are defined by HA component taking into account also banking holidays, etc.

Once TRV target temperature is changed out of the schedule (manually at TRV or from HA GUI) this is detected by Schedy and for configured interval no changes are automatically handled. Once interval expires operation returns to scheduled behavior.

Window opening / closing is managed by Schedy. There exist artificial binary sensors in HASS to introduce a delay into any reaction of window close / open. To not exhaust TRV batteries for short events.

In the HA setting there are parameters specifying if heating season is in place and if some vacation or other home leave is happening. Based on this heating is enabled or not and Schedy is taking this into account. Also there is an option to force working day or in opposite the free day and override the calendar.

Decision diagram for each room looks like that:
![Heating schema](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/heating.png)

## Screenshots

The general overview status:
![Main dashboard](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardmain.png)

Setting temperatures and schedules for one room:
![Temperature setting](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardsettemp.png)

Some settings for kiosk mode:
![Service tab](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardservice.png)

And the same in night view:
![Main dashboard](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardmaindark.png)

![Temperature setting](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardsettempdark.png)

![Service tab](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardservicedark.png)
