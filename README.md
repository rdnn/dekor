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

### Turn Lights On / Off
`+;1;SETSTATE;{ON/OFF};{UNIX TIMESTAMP};{CRC32}`

### Set Lighting Level
`+;1;SETLEVEL;{0-100};{UNIX TIMESTAMP};{CRC32}`

### Rename Lights
`+;1;SET;NAME,{NAME};{UNIX TIMESTAMP};{CRC32}`

## Examples
### Turn Lights On
````
$fp = fsockopen('10.0.0.X', 4001, $errno, $errstr, 30);

$command = sprintf('+;1;SETSTATE;%s;%d;',
    'ON',
    time()
);

fwrite($fp, $command . crc32($command) . "\n");

fclose($fp);
````

### Set Lighting Level to 50%
````
$fp = fsockopen('10.0.0.X', 4001, $errno, $errstr, 30);

$command = sprintf('+;1;SETLEVEL;%d;%d;',
    50,
    time()
);

fwrite($fp, $command . crc32($command) . "\n");

fclose($fp);
````
