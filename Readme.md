# X-Plane Garmin GPS Connector
This application provides two functions:
 - AviationIn message capture from X-Plane
 - Garmin GPS Hardware Connector for Mac (or Linux) computers.
 
## AviationIn Format Message Capture 
To capture GPS output messages, this application connects to a running X-Plane instance to set the position of the selected aircraft to known locations. The resulting GPS output from X-Plane is captured via a serial interface (windows only) and stored for analysis.

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

## X-Plane Garmin GPS Connector

This connector depends on the XPC [X-Plane Connect](https://github.com/nasa/XPlaneConnect) created by NASA for research.
