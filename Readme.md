# X-Plane Garmin GPS Connector
This application provides two functions:
 - Garmin GPS Hardware Connector for Mac (or Linux) computers
 - Garmin AviationIn message capture from X-Plane.

## Python Requirements
This application is written in Python version 3.x and has the following code library dependencies:
- click~=7.1.2
- pyserial~=3.5
- PyYAML~=5.4.1
- xpc


## X-Plane Garmin GPS Connector

This connector depends on the XPC [X-Plane Connect](https://github.com/nasa/XPlaneConnect) created by NASA.
The X-Plane Connect (XPC) Toolbox is an open source research tool used to interact with X-Plane.
Make sure you have the XPC plugin installed and enabled in your X-Plane application. This plugin is what allows this 
application to query the simulation for the data needed to drive your GPS device.

You need a serial cable connected between your X-Plane simulation machine and your physical Garmin GPS.
It is okay to use an RS232 to USB adapter if you have a Macintosh computer. The USB adapter will show up in the devices 
directory e.g., `/dev/cu.usbserial-143120` (I use a Prolific Serial/USB adapter). 

Don't forget to modify the `config.yml` file with your serial device. 

The Network/Loopback interface is used to periodically query the running simulation for position
and speed data. This data is formatted into a valid Garmin AviationIn formatted message and sent via the
serial connection to your GPS device. Note you can run this application on a different machine to the one running X-Plane.

You must connect your GPS to the machine running this connector.

To run this application, first start X-Plane then run `GarminGpsConnector.py`.

I.e., `python3 GarminGpsConnector.py`

## Garmin AviationIn Format Message Capture 
Finding information online for the Garmin AviationIn message format was too hard. Garmin has not published details of 
this serial protocol. 

Knowing that X-Plane did have Windows only support for Garmin GPS hardware, I created a serial message capture
utility that connects to the PC serial port and saves the data sent from the Windows version of X-Plane. This allowed 
me to determine what the protocol was and how to create it.

You can run this function by enabling it with the `--mode monitor` command line option when running `GarminGpsConnector.py`.

I.e., `python3 GarminGpsConnector.py --mode monitor`

Monitoring Configuration:
![Monitoring Setup](https://github.com/ReeceRobinson/garmin_gps_connector/blob/master/MonitorSetup.png)

The following image and ASCII figures show a capture of a single message.

Byte View:

![Message Structure Example](https://github.com/ReeceRobinson/garmin_gps_connector/blob/master/Message%20Structure%20Example.png)

ASCII View:
```
z00000
AS 00 0000
BW 000 0000
C000
D000
E00000
GR0000
I0000
KL0000
QW000
S-----
T---------
w01@
```
The message capture above captures one whole message that starts with `STX` (0x02) and ends with `ETX` (0x03).
Each data item is separated with carriage return followed by line feed (0x0D 0x0A).

The message elements are listed below:

| Prefix      | Description | Data Type       | Example    | Used?        |
| ----------- | ----------- | --------------- | ---------- | ------------ |
| z           | Altitude    | feet            | 00003      | Y            |
| A           | Latitude    | Hemi Deg MinSec | S 37 5135  | Y            |
| B           | Longitude   | Hemi Deg MinSec | E 174 4844 | Y            |
| C           | Compass     | Deg             | 201        | Y            |
| D           | Air Speed   | Knots           | 95         | Y            |
| E           | ?           | ?               | 00000      | N (Constant) |
| G           | ?           | ?               | R0000      | N (Constant) |
| I           | ?           | ?               | 0000       | N (Constant) |
| K           | ?           | ?               | L0000      | N (Constant) |
| Q           | Mag Variation| W/E DegDec     | E228       | Y            |
| S           | ?           | ?               | -----      | N (Constant) |
| T           | ?           | ?               | ---------  | N (Constant) |
| w           | ?           | ?               | 01@        | Y? (Constant)|

