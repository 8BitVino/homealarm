# DIY Automated home alarm system

Home assistant automations leveraging Unifi presence sensors for Smartphones to automate a home alarm.

Prereqs:
* Home assistant 
	* Multiple binary enabled sensors (door, windows, PIR's)
	* Integration - SMTP   
	* Integration - Unifi UCG Ultra or similar Unifi router


How it works?

(Diagram here)

![Flow diagram](https://github.com/8BitVino/homealarm/blob/main/alarmflowdiagram.jpg)
![Unifi settings](https://github.com/8BitVino/homealarm/blob/main/alarmunifisettings.jpg)


Presteps 

1) Unifi router

An important step is to ensure that the ARP timers on the Unifi router are set low. This ensures that device presence sensing is low enough to properly detect when a smartphone has left.

Select Unifi devices -> UCG Ultra -> Settings
Under ARP Cache Timeout -> Custom -> 30 seconds

(diagram here)

Note:  Very infrequently, the Unifi Router will not detect that an iPhone has "left" the wifi network. This seems to be a quirk for iPhones and not for Android. When this happens, manually going to the device in the Unifi administration and selecting "reconnect" forces the disconnect.


2) Home assistant integrations

The automation is based on the following integrations being configured:
	1) SMTP - Provides outdoing emails to home users and integration to my SMS message provider
	2) Presence sensors - eg: various Shelly, MQTT, Tasmota, ESPHOME which are your sensors to be monitored.
	3) Unifi Network - Provides the smartphone as an entity that is tracked for presense in Home assistant.



**3) Automation templates**  
Alarm Engagement Trigger (alarm_enagement.yaml)

This automation runs every 2 minutes to check if all registered smartphones have left the home's WiFi range. When confirmed, it:

- Confirms all smartphones are away
- Activates some on/off flashes of hallway/garage lighting (as visual alert on the typical exit locations)
- Enables the alarm notification automation.

The reason there is a 2 minute delay is to ensure that a false activation such a brief disconnect/reconnect of phone (say if someone reboots their phone) doesn't engage the alarm.


**2) Alarm Notifications**  
(alarm_notification.yaml)
Once enabled, this automation:

- Monitors for binary sensor triggers (motion, door, window sensors)
- Implements a 2-minute delay for verification (required due to delays in Wifi connections)
- Confirms that no smartphones are inside the home
- Dispatches both email and SMS alerts to home's occupants

The reason for a 2 minute delay in sending the alarm is that it's possible for a home user to get to an entry point to house and trigger an entry sensor BEFORE there is time for their smartphone to connect.

**2) Alarm Disengagement Trigger**  
(alarm_disengagement.yaml)
Running on a 2-minute cycle, this automation:

- Detects when any registered home smartphone returns home
- Sends an  email notification that alarm status disengaged
-  Activates some on/off flashes of hallway/garage lighting (as visual alert on the typical exit locations)
- Disables the alarm notification automation.

I decided to put this automation on a two minute cycle for checking. You could have it running more frequently, but it works well as is.

Enjoy this automation!
