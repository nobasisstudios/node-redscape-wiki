This flow illustrates how to trigger a chance event based on a given probability. 
The desired percentage chance of the event occurring is set in the change node, expressed as a value between 0 - 100. Each time the flow is called (e.g. via an inject node set to repeat at a fixed interval), a function node calculates a random value between 0 and 100. If the value is less than the specified probability, flow continues out of the top output. If it is greater, then flow continues out of the bottom output.

![Random Chance Flow](https://github.com/playfultechnology/propcontrol/blob/master/Documentation/screenshots/flow_chance.png)

```
[
    {
        "id": "576892a1.d28e94",
        "type": "function",
        "z": "729e4378.2c89bc",
        "name": "Evaluate chance",
        "func": "// Generate a random number between 0 - 100\nrnd = Math.round(Math.random() * 100);\n\n// If number is less than the desired probability\n// set in the previous node\nif(rnd < msg.probability) {\n    // Continue the flow on the top output\n    return [msg, null];\n}\nelse {\n    // Continue the flow on the bottom output\n    return [null, msg];\n}",
        "outputs": 2,
        "noerr": 0,
        "x": 1010,
        "y": 640,
        "wires": [
            [
                "bc757f01.723eb8"
            ],
            [
                "70318ba0.fc94a4"
            ]
        ]
    },
    {
        "id": "96a48c66.8a2b7",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Inject node set to repeat every x seconds",
        "info": "",
        "x": 440,
        "y": 600,
        "wires": []
    },
    {
        "id": "5fc004bb.0c1734",
        "type": "change",
        "z": "729e4378.2c89bc",
        "name": "Set event probability",
        "rules": [
            {
                "t": "set",
                "p": "probability",
                "pt": "msg",
                "to": "50",
                "tot": "num"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 754,
        "y": 639,
        "wires": [
            [
                "576892a1.d28e94"
            ]
        ],
        "info": "(% chance of occurring each second)"
    },
    {
        "id": "fda1fd41.742188",
        "type": "inject",
        "z": "729e4378.2c89bc",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "1",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "x": 490,
        "y": 640,
        "wires": [
            [
                "5fc004bb.0c1734"
            ]
        ]
    },
    {
        "id": "7272c004.9a8688",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Set probability of event (0-100)",
        "info": "",
        "x": 770,
        "y": 600,
        "wires": []
    },
    {
        "id": "e26d8892.b4e9a8",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Route output",
        "info": "",
        "x": 1010,
        "y": 600,
        "wires": []
    },
    {
        "id": "bc757f01.723eb8",
        "type": "link out",
        "z": "729e4378.2c89bc",
        "name": "Success",
        "links": [],
        "x": 1175,
        "y": 580,
        "wires": []
    },
    {
        "id": "70318ba0.fc94a4",
        "type": "link out",
        "z": "729e4378.2c89bc",
        "name": "Failure",
        "links": [],
        "x": 1175,
        "y": 700,
        "wires": []
    },
    {
        "id": "5031863e.0c9328",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Probability passed",
        "info": "",
        "x": 1250,
        "y": 540,
        "wires": []
    },
    {
        "id": "1126e343.fee05d",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Probability failed",
        "info": "",
        "x": 1240,
        "y": 660,
        "wires": []
    }
]
```