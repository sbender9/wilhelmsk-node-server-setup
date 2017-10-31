# wilhelmsk-node-server-setup
Examples and Instructions to setup signalk-node-server to support all of the WilhelmSK features


First you'll  want to make sure you have the latest and greatest [signalk-server-node](https://github.com/SignalK/signalk-server-node) installed. Please see instructions there for initial install.

You will also need to have the latest and greatest canboat "analyzer" from https://github.com/canboat/canboat

You'll probably want to install mdns so that WilhelmSK can discover your boat:

```
cd /usr/local/src/signalk-server-node
npm install mdns
```

If you want to be able to control a Raymarine Autopilot or Fusion Stereo, you'll need to update your node server settings to include config for writing to the N2K bus. For your actisense provider, include the "toChildProcess" option and set it to "nmea2000out".There a full settings example in the repository above.

```
  "id": "actisense",
  "pipeElements": [{
    "type": "providers/execute",
    "options": {
      "command": "actisense-serial /dev/ttyUSB0",
      "toChildProcess": "nmea2000out"
    }
  }
```

Next install the node server plugins that you want. Go to <http://localhost:3000/appstore> and install:

```
signalk-raymarine-autopilot
signalk-fusion-stereo
signalk-push-notifications
signalk-alarm-silencer
signalk-wilhelmsk-plugin
```

Now restart your server and go to <http://localhost:3000/plugins/configure>.
Activate the "Edit Zones", "Push Notifications", "Raymarine Autopilot", "Alarm Silencer", "Fusion Stereo" and "WilhelmSK" plugins.

For the autopilot and fusion plugins, you need to find the N2K device ids for those devices.
Hit the rest api for your server at <http://localhost:3000/signalk/v1/api/vessels/self>. You can do this in a browser, using curl or by looking at the "Log" in WilhelmSK.

You're looking for a piece of data in SignalK that comes from the device. For example, I know that pitch, raw and roll come from the Raymarine Course Computer:

```
  "navigation": {
    "attitude": {
      "$source": "actisense.204", 
      "pgn": 127257, 
      "pitch": 0.0219, 
      "roll": 0.0328, 
      "timestamp": "2016-11-15T19:59:59.891Z", 
      "yaw": 2.0521
    }, 
```

It's the '"$source": "actisense.204"' entry that's important. In this case, the device id is 204. Enter this for "Autopilot N2K Device ID" in the plugin configuration screen and hit Submit.

#Optionally install the anchor alarm plugin via the app store:

```
signalk-anchoralarm-plugin
```


***Please note that this gives anyone with access to your boat network and the signalk-node-server the ability to steer your boat. To be safe you can 'npm uninstall signalk-raymarine-autopilot' when not in use. Security and Authentication is coming soon to node server***


#Links
* [signalk-anchoralarm-plugin git](https://github.com/sbender9/signalk-anchoralarm-plugin)
* [signalk-raymarine-autopilot' git](https://github.com/sbender9/signalk-raymarine-autopilot)
* [signalk-fusion-stereo git](https://github.com/sbender9/signalk-fusion-stereo)
* [SignalK.org](http://www.signalk.org)


