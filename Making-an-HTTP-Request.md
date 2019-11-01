Devices such as ESP8266-powered Wemos D1, ESP32, or Raspberry Pis are capable of running simple local web servers. One method to control such network-connected devices is for Node-RED to issue a GET request to an HTTP endpoint on that device. Parameters can be appended to the URL to issue commands, and the device can issue a response in the HTML of the reply.

![HTTP Request](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/HTTP_request.jpg)

This recipe illustrates how to issue HTTP GET request every minute, which passes two variables representing the hour and minute of the current time. This request is sent to a webpage running on a Wemos D1 Mini, which retrieves the variables sent in the URL string and uses them to set the position of clock hands attached to a dual-shaft stepper motor.

## Wiring
![HTTP Request](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/wiring/WemosD1Mini_DualShaftStepper_bb.jpg)

## Flow
![HTTP Clock Flow](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_clock_HTTP.png)

```
[
    {
        "id": "6bba2de2.eb5254",
        "type": "http request",
        "z": "a750a527.4cc178",
        "name": "",
        "method": "GET",
        "ret": "txt",
        "paytoqs": false,
        "url": "http://192.168.1.4?h={{{h}}}&m={{{m}}}",
        "tls": "",
        "persist": false,
        "proxy": "",
        "authType": "",
        "x": 710,
        "y": 260,
        "wires": [
            []
        ]
    },
    {
        "id": "e418a358.a0bfc",
        "type": "function",
        "z": "a750a527.4cc178",
        "name": "Get current time",
        "func": "// Retrieve current time\nvar now = new Date();\n// Extract the hour and minute components\nvar h = now.getHours();\nvar m = now.getMinutes();\n// Add the time elements to the message object\nmsg.h = h;\nmsg.m = m;\n// And pass along the flow\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 500,
        "y": 260,
        "wires": [
            [
                "6bba2de2.eb5254"
            ]
        ]
    },
    {
        "id": "3e52d5cb.af4d82",
        "type": "inject",
        "z": "a750a527.4cc178",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "60",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "x": 290,
        "y": 260,
        "wires": [
            [
                "e418a358.a0bfc"
            ]
        ]
    },
    {
        "id": "611b7992.0dd6e8",
        "type": "comment",
        "z": "a750a527.4cc178",
        "name": "Trigger every minute",
        "info": "",
        "x": 290,
        "y": 220,
        "wires": []
    },
    {
        "id": "bfbe44f4.c55398",
        "type": "comment",
        "z": "a750a527.4cc178",
        "name": "Set hour and minute",
        "info": "",
        "x": 510,
        "y": 220,
        "wires": []
    },
    {
        "id": "e4719459.c39c7",
        "type": "comment",
        "z": "a750a527.4cc178",
        "name": "Pass time parameters to GET request",
        "info": "",
        "x": 790,
        "y": 220,
        "wires": []
    }
]
```