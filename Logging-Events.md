It can be very useful to record a log of game events.
* Examining log entries can be used in diagnosis and troubleshooting of any game elements that are not working correctly 
* Individual game logs can be used to create a personalised report for each team - not only telling them the overall time it took to complete the game, but providing a breakdown of the time spent on each element, the number of attempts they had at each puzzle, etc.
* Logs can also be aggregated and summarised to inform amendments to the game design - are there puzzles which, on average, players are taking too long to solve, or are players repeatedly trying the same incorrect solution?

![Dashboard Log Display](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/dashboard_log.png)

This flow illustrates how to record a log of game events processed by the Node-REDscape server. The payload of any message may be logged by sending it to the a link out node connected to the "Log" link illustrated at the left hand-side of this flow. The time at which the event occurred, and any other additional information, is added by a function node before being sent to both an onscreen dashboard UI element, and also a log file saved on the local file system.
Logging may be enabled or disabled by a toggle switch, and there is a button to clear the current log display, both exposed as dashboard nodes.

![Example Logging Flow](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_logging.png)

```
[
    {
        "id": "278c2afe.bf826e",
        "type": "file",
        "z": "b006bace.6967b",
        "name": "Append to Log File",
        "filename": "",
        "appendNewline": true,
        "createDir": true,
        "overwriteFile": "false",
        "encoding": "none",
        "x": 1030,
        "y": 400,
        "wires": [
            []
        ]
    },
    {
        "id": "5f2d3470.869e54",
        "type": "ui_switch",
        "z": "b006bace.6967b",
        "name": "",
        "label": "Logging",
        "tooltip": "",
        "group": "70fab444.c86fcc",
        "order": 4,
        "width": 4,
        "height": 1,
        "passthru": false,
        "decouple": "false",
        "topic": "",
        "style": "",
        "onvalue": "true",
        "onvalueType": "bool",
        "onicon": "",
        "oncolor": "",
        "offvalue": "false",
        "offvalueType": "bool",
        "officon": "",
        "offcolor": "",
        "x": 560,
        "y": 100,
        "wires": [
            [
                "bb5b2186.ace36"
            ]
        ]
    },
    {
        "id": "bb5b2186.ace36",
        "type": "change",
        "z": "b006bace.6967b",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "logging",
                "pt": "global",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 750,
        "y": 100,
        "wires": [
            []
        ]
    },
    {
        "id": "a504759c.6ccbd8",
        "type": "switch",
        "z": "b006bace.6967b",
        "name": "",
        "property": "logging",
        "propertyType": "global",
        "rules": [
            {
                "t": "eq",
                "v": "true",
                "vt": "jsonata"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 1,
        "x": 470,
        "y": 340,
        "wires": [
            [
                "b5bc549b.7044"
            ]
        ]
    },
    {
        "id": "b5bc549b.7044",
        "type": "function",
        "z": "b006bace.6967b",
        "name": "Create Log Entry",
        "func": "// Define a payload object with the fields required\n// for the log entry\nvar log_entry = {\n    \"time\": new Date(),\n    \"topic\": msg.topic,\n    \"payload\": msg.payload\n};\nmsg.payload = log_entry;\n\n// Specify the log file\nmsg.filename = \"/Node-REDscape/log.txt\";\n\n// Show the log entry below the editor node\nnode.status({fill:\"green\", shape:\"dot\", text:JSON.stringify(log_entry)});\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 750,
        "y": 340,
        "wires": [
            [
                "278c2afe.bf826e",
                "a14b9d5d.4fa7c"
            ]
        ]
    },
    {
        "id": "766326d9.54d75",
        "type": "comment",
        "z": "b006bace.6967b",
        "name": "Toggle Logging On/Off",
        "info": "",
        "x": 600,
        "y": 60,
        "wires": []
    },
    {
        "id": "812435ed.a8e9f8",
        "type": "ui_template",
        "z": "b006bace.6967b",
        "group": "70fab444.c86fcc",
        "name": "Dashboard Log",
        "order": 3,
        "width": "30",
        "height": 2,
        "format": "<div ng-repeat=\"line in msg.payload track by $index\">\n  <div ng-style=\"{'margin-bottom':'1em'}\">{{line}}</div>\n</div>",
        "storeOutMessages": false,
        "fwdInMessages": false,
        "templateScope": "local",
        "x": 1240,
        "y": 280,
        "wires": [
            []
        ]
    },
    {
        "id": "a14b9d5d.4fa7c",
        "type": "function",
        "z": "b006bace.6967b",
        "name": "Update Log Display",
        "func": "// Retrieve the existing log display\nvar logDisplay = flow.get(\"logDisplay\") || [];\n\n// Add the new log entry to the top \nlogDisplay.unshift(msg.payload);\n\n// Cap maximum size of the log display\nif(logDisplay.length > 100) {\n    // Remove the last element\n    logDisplay.pop();\n}\n\n// Save the updated log of entries\nflow.set(\"logDisplay\", logDisplay);\n\n// Pass the display along the flow\nmsg.payload = logDisplay;\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1030,
        "y": 280,
        "wires": [
            [
                "812435ed.a8e9f8"
            ]
        ]
    },
    {
        "id": "e0feacbd.8df04",
        "type": "link in",
        "z": "b006bace.6967b",
        "name": "Log",
        "links": [
            "99b8c19a.35c048"
        ],
        "x": 255,
        "y": 340,
        "wires": [
            [
                "a504759c.6ccbd8"
            ]
        ]
    },
    {
        "id": "8e0765ca.4e1188",
        "type": "ui_button",
        "z": "b006bace.6967b",
        "name": "",
        "group": "70fab444.c86fcc",
        "order": 1,
        "width": 4,
        "height": 1,
        "passthru": false,
        "label": "Clear Log",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "true",
        "payloadType": "bool",
        "topic": "",
        "x": 560,
        "y": 220,
        "wires": [
            [
                "854c53dc.f2f568"
            ]
        ]
    },
    {
        "id": "854c53dc.f2f568",
        "type": "change",
        "z": "b006bace.6967b",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "logDisplay",
                "pt": "flow",
                "to": "[]",
                "tot": "json"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 750,
        "y": 220,
        "wires": [
            [
                "a14b9d5d.4fa7c"
            ]
        ]
    },
    {
        "id": "19aa5f56.06a0c1",
        "type": "comment",
        "z": "b006bace.6967b",
        "name": "Clear the Log Display",
        "info": "",
        "x": 600,
        "y": 180,
        "wires": []
    },
    {
        "id": "536d9c79.18e9ec",
        "type": "comment",
        "z": "b006bace.6967b",
        "name": "Log event",
        "info": "",
        "x": 260,
        "y": 300,
        "wires": []
    },
    {
        "id": "dabcf3ed.663f38",
        "type": "comment",
        "z": "b006bace.6967b",
        "name": "Only continue if logging enabled",
        "info": "",
        "x": 490,
        "y": 300,
        "wires": []
    },
    {
        "id": "8b731b49.a3f12",
        "type": "comment",
        "z": "b006bace.6967b",
        "name": "Specify fields to log",
        "info": "",
        "x": 750,
        "y": 300,
        "wires": []
    },
    {
        "id": "9049b386.60d028",
        "type": "comment",
        "z": "b006bace.6967b",
        "name": "Send Log to Dashboard",
        "info": "",
        "x": 1050,
        "y": 240,
        "wires": []
    },
    {
        "id": "26cb14bf.ccfb24",
        "type": "comment",
        "z": "b006bace.6967b",
        "name": "Send Log to File",
        "info": "",
        "x": 1020,
        "y": 360,
        "wires": []
    },
    {
        "id": "70fab444.c86fcc",
        "type": "ui_group",
        "z": "",
        "name": "Default",
        "tab": "1bdacd1f.24a6bb",
        "disp": false,
        "width": "30",
        "collapse": false
    },
    {
        "id": "1bdacd1f.24a6bb",
        "type": "ui_tab",
        "z": "",
        "name": "Logging",
        "icon": "dashboard",
        "order": 2,
        "disabled": false,
        "hidden": false
    }
]
```