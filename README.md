# Home HASS project
This is home automation for heating control and catching various home alerts using Home Assistant and some additional components.

## Purpose
I have just few main goals to reach initially (2018):
 - Receive alerts from smoke sensors and water flood sensors and send warning immediately
 - Replace old thermostatic radiator valves (TRV) with connected ones
 - Control heating remotely
 - Do not rely on cloud controlled device and keep everything on local network

Once main goals are fulfilled few more goals appears (2019) :wink:
 - Handle easily open / close window events and get adequate heating reaction
 - Handle manual intervention on TRVs somehow logically
 - Do not hardcode heating schedules to config files but provide UI
 - Do not send data to China or anywhere else if it can be avoided

And then air-conditioning comes in place (2020)  :snowflake:
 - Integrate air-conditioning and let it be autonomous
 - Different logic needed compared to heating
 - Let UI show either heating or cooling settings only based on seasons

## Overview
Whole solution is using quite common components.
On software side:
 - Home Assistant (https://www.home-assistant.io/)
 -- many plugins to Home Assistant via HACS (https://github.com/hacs)
 - AppDaemon (https://github.com/home-assistant/appdaemon)
 - Zigbee2MQTT (https://www.zigbee2mqtt.io/)
 - Mosquitto MQTT broker (https://mosquitto.org/)
 - Schedy for HA (https://github.com/efficiosoft/hass-apps)
 - CustomUI (https://github.com/andrey-git/home-assistant-custom-ui)

On HW side:

 - Xiomi gateway (ZigBee hub) - *no longer acting as a hub, just night light*
 - TI C1352R-2 (USB dev board for Zigbee network)
 - various Xiaomi Aqara detectors (smoke sensors, flood sensors, door contacts, thermometers)
 - Z-Wave Me UZB (USB stick for Z-Wave network)
 - EUROtronic Spirit Z-Wave (TRVs)
 - few more Z-Wave devices to extend coverage
 - Broadlink RM3 Mini (WiFi capable remote infra controller)
 - Samsung WindFree air-conditioner

Current status covered by this repo is  that all the goals are working.
Once any alert is triggered by smoke or flood sensor alerts are sent via Pushover.
All TRVs are connected and controlled by Home Assistant in cooperation with Schedy. All settings for temperature values and intervals are adjustable via Home Assistant GUI.
All air-conditioning units are controlled by Home Assistant in cooperation with Schedy via Broadlink remotes. All settings are adjustable via Home Assistant GUI.

## Heating
This is the complex part of the whole solution. So few remarks how it works and what are limitations (mostly intended)

Everything related to schedules is dynamically set in UI. Schedy is watching defined HASS entities and in case of a change of any entity it performs re-evaluation of the schedule. To handle the regular time based schedules there is an artificial sensor each minute changing itself to number of minutes since midnight. This way each minute there is a change and Schedy reacts.

It is possible to schedule two intervals of high temperature per day and room. For my use cases it is enough. Different setting can be specified for working and non-working days. Working days are defined by HA component taking into account also banking holidays, etc.

Once TRV target temperature is changed out of the schedule (manually at TRV or from HA GUI) this is detected by Schedy and for configured interval no changes are automatically handled. Once interval expires operation returns to scheduled behavior.

Window opening / closing is managed by Schedy. There exist artificial binary sensors in HASS to introduce a delay into any reaction of window close / open. To not exhaust TRV batteries for short events.

In the HA setting there are parameters specifying if heating season is in place and if some vacation or other home leave is happening. Based on this heating is enabled or not and Schedy is taking this into account. Also there is an option to force working day or in opposite the free day and override the calendar.

## Air-conditioning
This part of the solution utilitize a lot of mentioned above. Including vacation, working and free days or Schedy and its every minute reevaluation mechanism.
So just things specific to A/C described bellow.

Everything related to schedules is again dynamically set in UI. It is possible to set one interval of air-conditioning per day and room, for working and non-working days. There is specified a trigger temperature and once this is reached during the day, air-conditioning will happen in the defined interval.

In addtion to air-conditioning a lot of changes happend under the hood of Lovelace UI. As heating and cooling are happening in different seasons there is no need to show all controls in UI at the same time. Now there is defined interval when cooling controls shall be displayed instead of heating ones. A lot of conditional cards are used in the UI same as many vertical stacks to keep everything in the right positions.

---
Decision diagram for each room looks like this:
![Heating schema](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/logic.png)

## Screenshots

The general overview status:
![Main dashboard](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardmain.png)

Setting temperatures and schedules for heating of one room:
![Temperature setting](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardsettemp.png)

Setting temperatures and schedules for air-conditioning of one room:
![Temperature setting](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardsettempac.png)

Some settings for kiosk mode:
![Service tab](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardservice.png)

Automatic change of the main screen from heating to air-conditioning:
![Service tab](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardmainswitch.png)

And the main screen in night view:
![Main dashboard](https://raw.githubusercontent.com/tristone-cz/hass/master/mediafiles/boardmaindark.png)
