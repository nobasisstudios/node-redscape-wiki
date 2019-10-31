If you deliver a _buffer_ to the Audio Out node (i.e. an .MP3/.WAV file loaded using the File In node), it can be used to play an audio file. However, if you send a message payload containing a _text string_, the Audio Out node will invoke the text-to-speech engine and read out the message instead.

The following recipe illustrates this by implementing a countdown that reads out the time remaining every 10 seconds.

![Text-To-Speech Flow Example](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_audio_TTS.png)

```
[
    {
        "id": "890c0a62.f019b",
        "type": "ui_audio",
        "z": "5eb05cb4.d68fb4",
        "name": "",
        "group": "b0a7fccd.a7aad",
        "voice": "",
        "always": true,
        "x": 1320,
        "y": 320,
        "wires": []
    },
    {
        "id": "9e4ba821.b8172",
        "type": "comment",
        "z": "5eb05cb4.d68fb4",
        "name": "Inject node repeats every x seconds",
        "info": "",
        "x": 200,
        "y": 280,
        "wires": []
    },
    {
        "id": "64208f24.367",
        "type": "function",
        "z": "5eb05cb4.d68fb4",
        "name": "Calculate time remaining",
        "func": "// Retrieve the time which this flow was started, or if this\n// the first run, get the timestamp from the previous node\nvar startTime = context.get(\"startTime\") || msg.payload;\n\n// Update the stored value\ncontext.set(\"startTime\", startTime);\n\n// Now get the current time\nvar currentTime = msg.payload;\n\n// Calculate how much time has elapsed (in seconds)\nvar elapsedTime = (currentTime - startTime) / 1000;\n\n// Calculate time remaining\nvar remainingTime = 60 - elapsedTime;\n\n// Round to the nearest 10 seconds\nvar roundedTime = Math.round(remainingTime / 10) * 10;\n\nmsg.roundedTime = roundedTime;\n\n// Update editor status\nnode.status({fill:\"grey\",shape:\"dot\",text:msg.roundedTime});\n\n// Stop sending messages after time runs out\nif(roundedTime >= 0) {\n    return msg;\n}",
        "outputs": 1,
        "noerr": 0,
        "x": 490,
        "y": 320,
        "wires": [
            [
                "1e3bfdc1.1e4b42"
            ]
        ]
    },
    {
        "id": "53b945a3.5dc424",
        "type": "inject",
        "z": "5eb05cb4.d68fb4",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "1",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "x": 230,
        "y": 320,
        "wires": [
            [
                "64208f24.367"
            ]
        ]
    },
    {
        "id": "1e3bfdc1.1e4b42",
        "type": "rbe",
        "z": "5eb05cb4.d68fb4",
        "name": "",
        "func": "rbe",
        "gap": "",
        "start": "",
        "inout": "out",
        "property": "roundedTime",
        "x": 750,
        "y": 320,
        "wires": [
            [
                "79e577f3.cca438"
            ]
        ]
    },
    {
        "id": "b9d37631.5fa948",
        "type": "comment",
        "z": "5eb05cb4.d68fb4",
        "name": "Trigger only when change",
        "info": "",
        "x": 770,
        "y": 280,
        "wires": []
    },
    {
        "id": "79e577f3.cca438",
        "type": "function",
        "z": "5eb05cb4.d68fb4",
        "name": "Form sentence",
        "func": "if(msg.roundedTime > 0) {\n    msg.payload = \"You have \" + msg.roundedTime + \" seconds remaining\";\n}\nelse {\n    msg.payload = \"You have run out of time.\";\n}\n\n// Update editor status\nnode.status({fill:\"grey\",shape:\"dot\",text:msg.payload});\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1040,
        "y": 320,
        "wires": [
            [
                "890c0a62.f019b"
            ]
        ]
    },
    {
        "id": "934f8d8c.da3b1",
        "type": "comment",
        "z": "5eb05cb4.d68fb4",
        "name": "Create phrase based on time remaining",
        "info": "",
        "x": 1070,
        "y": 280,
        "wires": []
    },
    {
        "id": "691c3255.30ecfc",
        "type": "comment",
        "z": "5eb05cb4.d68fb4",
        "name": "Rounded to nearest 10secs",
        "info": "",
        "x": 500,
        "y": 280,
        "wires": []
    },
    {
        "id": "f3f4db7d.c5963",
        "type": "comment",
        "z": "5eb05cb4.d68fb4",
        "name": "Use Text-To-Speech engine",
        "info": "",
        "x": 1380,
        "y": 280,
        "wires": []
    },
    {
        "id": "b0a7fccd.a7aad",
        "type": "ui_group",
        "z": "",
        "name": "Game Status",
        "tab": "b191b70b.e2896",
        "order": 1,
        "disp": false,
        "width": "6",
        "collapse": false
    },
    {
        "id": "b191b70b.e2896",
        "type": "ui_tab",
        "z": "",
        "name": "Dashboard",
        "icon": "dashboard",
        "order": 1,
        "disabled": false,
        "hidden": false
    }
]
```