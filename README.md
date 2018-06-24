# Home HASS project
This is home automation for heating control and catching various home alerts using Home Assistant and some additional components.

## Purpose
I have just few main goals to reach initially:
 - Receive alerts from smoke sensors and water flood sensors and send warning immediately
 - Replace old thermostatic radiator valves (TRV) with connected ones
 - Control heating remotely

And as main goals are fulfilled few more goals appears :wink:
 - Handle easily open / close window events and get adequate heating reaction
 - Handle manual intervention on TRVs somehow logically
 - Do not hardcode heating schedules to config files but provide UI

## Overview
Whole solution is using quite common components. 
On software side:
 - Home Assistant (https://www.home-assistant.io/)
 - AppDaemon (https://github.com/home-assistant/appdaemon)
 - Heaty for HA (https://github.com/efficiosoft/hass-apps)
 - CustomUI (https://github.com/andrey-git/home-assistant-custom-ui)

On HW side:

 - Xiomi gateway (ZigBee hub)
 - various Xiaomi Aqara detectors (smoke sensors, flood sensors, door contacts, thermometers)
 - Z-Wave Me UZB (USB stick for Z-Wave network)
 - EUROtronic Spirit Z-Wave (TRVs)
 - few more Z-Wave devices to extend coverage

Current status covered by this repo is  that all the goals are working.
Once any alert is triggered by smoke or flood sensor alerts are sent via Pushbullet.
All TRVs are connected and controlled by Home Assistant in cooperation with Heaty. All settings for temperature values and intervals are adjustable via Home Assistant GUI.

## Heating
This is the complex part of the whole solution. So few remarks how it works and what are limitations (mostly intended)

As everything is dynamically set in UI Heaty cannot manage all the schedules itself. So I have taken as acceptable granularity 10 minutes interval. This means that automatic changes of temperatures are possible only each hour at 00, 10, ... 50 minutes. At that time HA is triggering Heaty to evaluate current situation and potentially adjust TRVs.

It is possible to schedule two intervals of high temperature per day and room. For my use cases it is enough. Different setting can be specified for working and non-working days. Working days are defined by HA component taking into account also banking holidays, etc.

Once TRV target temperature is changed out of the schedule (manually at TRV or from HA GUI) this is detected and Heaty is not triggered until current interval ends or next interval starts. Then the override flag is removed and full automation is restored.

Window opening / closing is managed fully by Heaty itself, no specialties.

In the HA setting there are parameters specifying if heating season is in place and if some vacation or other home leave is happening. Based on this heating is enabled or not and Heaty is taking this into account.

Decision diagram for each room looks like that:
![Heating schema](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/heating.png)

## Screenshots

Sorry for the texts, all of them in Czech :relaxed: 

The general overview status:
![Main dashboard](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardmain.png)

Setting temperatures and schedules for one room:
![Temperature setting](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardsettemp.png)

System setting:
![Service tab](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardservice.png)

