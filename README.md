# Solis-4G RS485 - Openenergymonitor EmonHub
Python project to Read Solis-4G inverter over RS485 and report to EmonHub 

1. Save python script to ~/data/solis-4g.py
2. Configure Emonhub with the additional settings below
3. Call python file using Node-Red on emonpi / emon hub


## MinimalModbus installation
Set emonpi into read-write mode
Install minimalmodbus
Set emonpi to read-only mode

```
rpi-rw
sudo pip install -U minimalmodbus
rpi-ro
```

## EmonHub Configuration
Note the information must be in the correct section for emonhub to work.
Once the interfacer has been added, emonhub needs to be restarted.  
Node configuration updates will be detected when the configuration is updated.

1. Add to [interfacers] section:

```
[[mysocketlistener]]
        Type = EmonHubSocketInterfacer
        [[[init_settings]]]
                port_nb = 8080
        [[[runtimesettings]]]
                pubchannels = ToEmonCMS,
```
                

2. Add to [nodes] section:

```
[[3]]
    nodename = solis4g-kw
    [[[rx]]]
        names = AllTimeEnergyKW,TodayKW
        datacodes = I, H
        scales = 1,0.1
        units = kW,kW

[[4]]
    nodename = solis4g-realtime
    [[[rx]]]
        names = ACRealtimeW,RealtimeDCV,RealtimeDCI,InverterC,ACRealTimeF,ACRealTimeV,ACRealTimeI
        datacodes = I, H, H, H, H, H, H
        scales = 1,0.1,0.1,0.1,0.01,0.1,0.1
        units = W,V,A,C,Hz,V,A
```
        
## Node-Red Configuration
(Based on https://stackoverflow.com/questions/32057882/how-to-trigger-python-script-on-raspberry-pi-from-node-red)
In a new flow:
1. Add an inject node, set it to automatically inject at start and set the node to collect data every 5 seconds
2. Add an external exec node and configure it to call:
   python ~/data/solis-4g.py

