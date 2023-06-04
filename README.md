# MQTT Integration Tutorial

# Overview

This tutorial covers the MQTT topic structures and payload for both the bridge and 3rd party devices.

# Valid Topic from Bridge

Topics from the bridge follow this structure:

`ee/bridge/<serial>/<msg-type>/<device-type>/<room-name>/name>`

Where:

- ee is a constant prefix (representing eelectron)
- bridge identifies the source as a bridge
- <serial> is the serial number of the bridge
- <msg-type> can be sts or cmd (status or command)
- <device-type> is the category/type the device belongs to e.g. sensors or binary-sensors
- <room-name> identifies the room associated with the message
- <name> the name of the entity, e.g. setpoint or cortesy-light (lower case letters only)

# EXAMPLE

For a state update from the bridge to MQTT, the topic might be:

`ee/bridge/0066c35564/sts/1010/setpoint`

And the payload could be:

`{ "value": 22 }`

For a command from MQTT to the bus, the topic could be:

`ee/bridge/0066c35564/cmd/sensors/1010/setpoint`

And the payload would be the desired setpoint, for example:

`22`

An RPC response from the node to MQTT could have this topic:

`ee/bridge/0066c35564/sts/1010/setpoint/response`

With a payload indicating receipt of the command:

`{ "rpcReceived": "OK" }`

# Valid Topic from 3rd Party

Topics from other devices follow a slightly different structure:

`other/device/<serial>/<msg-type>/<device-type>/<room-name>/name>`

This structure is similar to that for the bridge, but the prefix is 'other/device' instead of 'ee/bridge'.

# Valid Payload

Regardless of the source, valid payloads are JSON objects containing a single value property:

`{ "value": <value> }`

Where <value> is the desired setpoint or 1 or 0 if it's a light etc.

# Summary

This structure allows for clear and organized communication between devices and a central MQTT server. Messages can be easily directed to and from specific devices, and their content can be readily understood and acted upon.

# Helper functions

in order to extract the correct name from the topic, it's important that the last part of the topic remains the name for all topics that's not related to an RPC response:

```javascript
var topicParts = metadata.topic.split("/");
var dataType = topicParts[topicParts.length - 1]; // get last part of topic
var telemetry = getTelemetry(data, dataType);

function getTelemetry(data, dataType) {
  var telemetry = {};
  telemetry[dataType] = data.value;
  return telemetry;
}
```
