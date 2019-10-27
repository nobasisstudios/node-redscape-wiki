This flow illustrates how to trigger events based on how much time has elapsed in the game. The function node compares the current timestamp (sent from the inject node at the beginning of the flow) to the starting timestamp at which the flow was first called, to determine the elapsed time in milliseconds since game start.
Based on this value, it assigns a "chapter" number to the game in progress, and passes this along the flow in the msg.chapter context. The rbe node is used to test when this chapter number changes, and therefore triggers one of the final four output nodes on each chapter start event.

![Chapter Flow](https://github.com/playfultechnology/propcontrol/blob/master/Documentation/screenshots/flow_chapter.png)
```
[
    {
        "id": "96a48c66.8a2b7",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Inject node set to repeat every x seconds",
        "info": "",
        "x": 700,
        "y": 740,
        "wires": []
    },
    {
        "id": "92107902.2829a8",
        "type": "function",
        "z": "729e4378.2c89bc",
        "name": "Assign chapter number",
        "func": "// Retrieve the time which this flow was started, or if this\n// the first run, get the timestamp from the previous node\nvar startTime = context.get(\"startTime\") || msg.payload;\n\n// Update the stored value\ncontext.set(\"startTime\", startTime);\n\n// Now get the current time\nvar currentTime = msg.payload;\n\n// Calculate how much time has elapsed\nvar elapsedTime = currentTime - startTime;\n\n// Now, determine which \"chapter\" of the story we're in, based \n// on the number of milliseconds elapsed\n// First ten seconds\nif(elapsedTime < 10000) {\n    msg.chapter = 1; \n}\n// Between ten seconds and one minute\nelse if(elapsedTime < 60000) {\n    msg.chapter = 2;\n}\n// Between one minute and 5 minutes\nelse if(elapsedTime < 300000) {\n    msg.chapter = 3;\n}\n// Any time after 5 minutes\nelse {\n    msg.chapter = 4;\n}\n\n// Update editor status\nnode.status({fill:\"grey\",shape:\"dot\",text:\"Chapter \" + msg.chapter});\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1030,
        "y": 780,
        "wires": [
            [
                "39996e5e.7588c2"
            ]
        ]
    },
    {
        "id": "546ae3ef.3bd5fc",
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
        "x": 710,
        "y": 780,
        "wires": [
            [
                "92107902.2829a8"
            ]
        ]
    },
    {
        "id": "39996e5e.7588c2",
        "type": "rbe",
        "z": "729e4378.2c89bc",
        "name": "",
        "func": "rbe",
        "gap": "",
        "start": "",
        "inout": "out",
        "property": "chapter",
        "x": 1310,
        "y": 780,
        "wires": [
            [
                "772f4447.a6df6c"
            ]
        ]
    },
    {
        "id": "2df4c701.e7a088",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Chapter # based on elapsed time",
        "info": "",
        "x": 1030,
        "y": 740,
        "wires": []
    },
    {
        "id": "6b8dd8d1.dae5",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Trigger only on chapter change",
        "info": "",
        "x": 1330,
        "y": 740,
        "wires": []
    },
    {
        "id": "772f4447.a6df6c",
        "type": "switch",
        "z": "729e4378.2c89bc",
        "name": "",
        "property": "chapter",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "1",
                "vt": "num"
            },
            {
                "t": "eq",
                "v": "2",
                "vt": "num"
            },
            {
                "t": "eq",
                "v": "3",
                "vt": "num"
            },
            {
                "t": "eq",
                "v": "4",
                "vt": "num"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 4,
        "x": 1530,
        "y": 780,
        "wires": [
            [
                "d84d1718.4813c"
            ],
            [
                "efbc696.a004298"
            ],
            [
                "1a4e66c7.8537b1"
            ],
            [
                "dfd1073f.ecbbd"
            ]
        ]
    },
    {
        "id": "d84d1718.4813c",
        "type": "link out",
        "z": "729e4378.2c89bc",
        "name": "Chapter 1 Start",
        "links": [],
        "x": 1695,
        "y": 680,
        "wires": []
    },
    {
        "id": "efbc696.a004298",
        "type": "link out",
        "z": "729e4378.2c89bc",
        "name": "Chapter 2 Start",
        "links": [],
        "x": 1695,
        "y": 740,
        "wires": []
    },
    {
        "id": "1a4e66c7.8537b1",
        "type": "link out",
        "z": "729e4378.2c89bc",
        "name": "Chapter 3 Start",
        "links": [],
        "x": 1695,
        "y": 800,
        "wires": []
    },
    {
        "id": "dfd1073f.ecbbd",
        "type": "link out",
        "z": "729e4378.2c89bc",
        "name": "Chapter 4 Start",
        "links": [],
        "x": 1695,
        "y": 860,
        "wires": []
    },
    {
        "id": "e9f45059.fde258",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Chapter 1 Start",
        "info": "",
        "x": 1800,
        "y": 680,
        "wires": []
    },
    {
        "id": "9d99f6cc.12ad98",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Chapter 2 Start",
        "info": "",
        "x": 1800,
        "y": 740,
        "wires": []
    },
    {
        "id": "4ff31376.2709a4",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Chapter 3 Start",
        "info": "",
        "x": 1800,
        "y": 800,
        "wires": []
    },
    {
        "id": "ea432342.df39e",
        "type": "comment",
        "z": "729e4378.2c89bc",
        "name": "Chapter 4 Start",
        "info": "",
        "x": 1800,
        "y": 860,
        "wires": []
    }
]
```  