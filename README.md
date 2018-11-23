# dekor
Reverse engineering of the DEKOR Lighting Protocol which is used to control LED lights connected to the [DEKOR EZ WiFi Controller](http://www.dekorlighting.com/store/our-collections/led-lighting-controllers/ez-wifi-dimmer-lighting-controller.html).

## Protocol Specifications
The DEKOR Lighting Protocol is sent entirely in plain text over TCP port 4001 to the DEKOR EZ WiFi Controller. Every command requires a UNIX timestamp and a CRC32 hash appended to every command. Of importance, the CRC32 hash *must* include the semicolon before the CRC32 hash. See examples for more information.

## Available Commands
### Get Controller Information
`+;1;GETINFO;1;{UNIX TIMESTAMP};{CRC32}`

### Get Controller Serial Number / Version
`+;1;GETINFO;1;{UNIX TIMESTAMP};{CRC32}`

### Get Lighting Schedule
`+;1;GETSCHED;0;{UNIX TIMESTAMP};{CRC32}`

### Set Lighting Schedule
`+;1;SETSCHED;0,{ON TIME},{OFF TIME},{LIGHTING LEVEL},{DAYS OF WEEK};{UNIX TIMESTAMP};{CRC32}`

| Parameter        | Description                                | Valid Values  |
| ---------------- | ------------------------------------------ | ------------- |
| ON TIME          | Schedule lights on at this time.           | 0000-2359     |
| OFF TIME         | Schedule lights off at this time.          | 0000-2359     |
| LIGHTING LEVEL   | Schedule lights on at this lighting level. | 0-100         |
| DAYS OF WEEK     | Repeat lighting schedule these days.       | MTWRFSN       |

### Turn Lights On / Off
`+;1;SETSTATE;{ON/OFF};{UNIX TIMESTAMP};{CRC32}`

### Set Lighting Level
`+;1;SETLEVEL;{0-100};{UNIX TIMESTAMP};{CRC32}`

### Rename Lights
`+;1;SET;NAME,{NAME};{UNIX TIMESTAMP};{CRC32}`

## Examples
### Set Light State
This example will turn the lights on.

````php
$fp = fsockopen('10.0.0.X', 4001, $errno, $errstr, 30);

$command = sprintf('+;1;SETSTATE;%s;%d;',
    'ON',
    time()
);

fwrite($fp, $command . crc32($command) . "\n");

fclose($fp);
````

### Set Lighting Level
This example will set the lighting level to 50%.

````php
$fp = fsockopen('10.0.0.X', 4001, $errno, $errstr, 30);

$command = sprintf('+;1;SETLEVEL;%d;%d;',
    50,
    time()
);

fwrite($fp, $command . crc32($command) . "\n");

fclose($fp);
````

### Set Lighting Schedule
This example will set the lighting schedule to repeat all days of the week (MTWRFSN) with an on time of 1600 and an off time of 0730 with an initial lighting level if 100%.

````php
$fp = fsockopen('10.0.0.X', 4001, $errno, $errstr, 30);

$command = sprintf('+;1;SETSCHED;0,%d,%d,%d,%s;%d;',
    1600,
    0730,
    100,
    'MTWRFSN,
    time()
);

fwrite($fp, $command . crc32($command) . "\n");

fclose($fp);
````
