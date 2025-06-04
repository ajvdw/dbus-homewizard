# DBus-Homewizard
Integrate Homewizard Energy P1 meter into [Victron Energies Venus OS](https://github.com/victronenergy/venus)

## Purpose
With the scripts in this repo it should be easy possible to install, uninstall, restart a service that integrates Homewizard P1 meter into VenusOS and GX devices from Victron.


## Inspiration
- https://github.com/fabian-lauer/dbus-shelly-3em-smartmeter

## How it works
### My setup
- Homewizard Energy P1 with latest firmware 
  - 1 or 3-Phase installation (normal for Netherlands)
  - Connected to network
  - IP 192.168.2.13/24  
- Victron Energy Multiplus II GX with Venus OS
  - Connected to network
  - IP 192.168.2.20/24

### Details / Process
So what is the script doing:
- Running as a service
- connecting to DBus of the Venus OS `com.victronenergy.grid.http_40` or `com.victronenergy.pvinverter.http_40`
- After successful DBus connection P1 meter is accessed via REST-API - simply the /status is called and a JSON is returned with all details
- Paths are added to the DBus with default value 0 - including some settings like name, etc
- After that a "loop" is started which pulls P1 data every 750ms from the REST-API and updates the values in the DBus

Thats it 😄

### Pictures
![Tile Overview](img/VenusOs_Overview.png)
![Remote Console - Overview](img/VenusOs_DeviceList.png) 
![SmartMeter - Values](img/VenusOs_P1.png)
![SmartMeter - Device Details](img/VenusOs_Service.png)

## Install & Configuration
### Get the code
Just grap a copy of the main branche and copy them to `/data/dbus-Home-Wizzard-Energy-P1`.
After that call the install.sh script.

The following script should do everything for you:
```
wget https://github.com/ajvdw/dbus-homewizard/archive/refs/heads/main.zip
unzip main.zip "dbus-homewizard-main/*" -d /data
mv /data/dbus-homewizard-main /data/dbus-homewizard
chmod a+x /data/dbus-homewizrd/install.sh
/data/dbus-homewizard/install.sh
rm main.zip
```
⚠️ Check configuration after that - because service is already installed an running and with wrong connection data (host, username, pwd) you will spam the log-file

### Change config.ini
Within the project there is a file `/data/dbus-Home-Wizzard-Energy-P1/config.ini` - just change the values - most important is the host, username and password in section "ONPREMISE". More details below:

| Section  | Config vlaue | Explanation |
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
