# DBus-Homewizard-P1
Integrate Homewizard Energy P1 meter into [Victron Energies Venus OS](https://github.com/victronenergy/venus)

## Purpose
With the scripts in this repo it should be easy possible to install, uninstall, restart a service that integrates Homewizard P1 meter into VenusOS and GX devices from Victron.


## Inspiration and based on
- https://github.com/back2basic/dbus-Home-Wizzard-Energy-P1
- https://github.com/fabian-lauer/dbus-shelly-3em-smartmeter

## How it works
### My setup
- Homewizard Energy P1 with latest firmware 
  - 1 or 3-Phase installation (normal for Netherlands)
  - Connected to network
  - IP 192.168.1.240/24  
- Victron Energy Multiplus II GX with Venus OS
  - Connected to network
  - IP 192.168.2.251/24 (venus.local)

### Details / Process
So what is the script doing:
- Running as a service
- connecting to DBus of the Venus OS `com.victronenergy.grid.http_40` 
- After successful DBus connection P1 meter is accessed via REST-API - simply the /status is called and a JSON is returned with all details
- Paths are added to the DBus with default value 0 - including some settings like name, etc
- After that a "loop" is started which pulls P1 data every 750ms from the REST-API and updates the values in the DBus

Thats it 😄

### Pictures
![Tile Overview](media/VenusOs_Overview.png)
![Remote Console - Overview](media/VenusOs_DeviceList.png) 
![SmartMeter - Values](media/VenusOs_P1.png)
![SmartMeter - Device Details](media/VenusOs_Service.png)

## Install & Configuration
### Get the code
Just grap a copy of the main branche and copy them to `/data/dbus-homewizard-p1`.
After that call the install.sh script.

The following script should do everything for you:
```
wget https://github.com/ajvdw/dbus-homewizard-p1/archive/refs/heads/main.zip
unzip main.zip "dbus-homewizard-p1-main/*" -d /data
mv /data/dbus-homewizard-p1-main /data/dbus-homewizard-p1
chmod a+x /data/dbus-homewizard-p1/install.sh
/data/dbus-homewizard-p1/install.sh
rm main.zip
```
⚠️ Check configuration after that - because service is already installed and running and with wrong connection data (host, loglevel) you will spam the log-file

### Change config.ini
Within the project there is a file `/data/dbus-homewizard-p1/config.ini` - just change the values - most important is the host. More details below:

| Section  | Config value | Description |
| ------------- | ------------- | ------------- |
| DEFAULT  | Host | IP or hostname of Homewizard P1 meter |
| DEFAULT  | SignOfLifeLog  | Time in minutes how often a status is added to the log-file `current.log` with log-level INFO |
| DEFAULT  | CustomName  | Name of your device - usefull if you want to run multiple versions of the script |
| DEFAULT  | DeviceInstance  | DeviceInstanceNumber e.g. 40 |
| DEFAULT  | LogLevel  | Define the level of logging - lookup: https://docs.python.org/3/library/logging.html#levels |
| DEFAULT  | Phases  | 1 for 1 phase system / 3 for 3 phase system |


## Used documentation
- https://github.com/victronenergy/venus/wiki/dbus#grid   DBus paths for Victron namespace GRID
- https://github.com/victronenergy/venus/wiki/dbus-api   DBus API from Victron
- https://www.victronenergy.com/live/ccgx:root_access   How to get root access on GX device/Venus OS
