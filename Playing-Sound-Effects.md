This recipe illustrates how to create a dashboard dropdown menu from which gamesmasters can select from a list of sound effects, and then use the Audio Out node to play the selected sound during the game. Pressing the "Play SFX" button loads the selected audio file (.WAV/.MP3) from the disk and plays it through the audio output of any device attached to the Node-REDscape server (note - this can, but doesn't have to be, the device that is running Node-REDscape itself - it can be any network-connected device that has the Node-RedSCAPE dashboard loaded in its browser.
 
![Dashboard Audio SFX](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/dashboard_audio_playsfx.png)

![Flow of Audio SFX](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_audio_sfx.png)

To use this flow, copy the following and paste into the "Import" tab of your Node-RED instance:
```
[
    {
        "id": "25a6f681.08178a",
        "type": "ui_dropdown",
        "z": "8f685eae.e4184",
        "name": "",
        "label": "",
        "tooltip": "",
        "place": "Select option",
        "group": "15fe206a.4a0578",
        "order": 4,
        "width": "0",
        "height": "0",
        "passthru": false,
        "options": [
            {
                "label": "Heavy Footsteps",
                "value": "C:\\Users\\alast\\Music\\sfx\\heavy_footsteps.wav",
                "type": "str"
            },
            {
                "label": "Door Creak",
                "value": "C:\\Users\\alast\\Music\\sfx\\gate_creak.mp3",
                "type": "str"
            },
            {
                "label": "Music Box",
                "value": "C:\\Users\\alast\\Music\\sfx\\musicbox.mp3",
                "type": "str"
            },
            {
                "label": "Floorboard creak",
                "value": "C:\\Users\\alast\\Music\\sfx\\floorboard_creak.wav",
                "type": "str"
            }
        ],
        "payload": "",
        "topic": "",
        "x": 160,
        "y": 340,
        "wires": [
            [
                "39dd601f.30ef1"
            ]
        ]
    },
    {
        "id": "9012ea18.f9e668",
        "type": "ui_button",
        "z": "8f685eae.e4184",
        "name": "",
        "group": "15fe206a.4a0578",
        "order": 5,
        "width": "0",
        "height": "0",
        "passthru": false,
        "label": "Play SFX",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "fa-volume-up",
        "payload": "",
        "payloadType": "str",
        "topic": "",
        "x": 160,
        "y": 440,
        "wires": [
            [
                "a1959f52.d3a478"
            ]
        ]
    },
    {
        "id": "848a56ea.2f36b",
        "type": "file in",
        "z": "8f685eae.e4184",
        "name": "",
        "filename": "",
        "format": "",
        "chunk": false,
        "sendError": false,
        "encoding": "none",
        "x": 550,
        "y": 440,
        "wires": [
            [
                "bc98c4f7.031da8"
            ]
        ]
    },
    {
        "id": "bc98c4f7.031da8",
        "type": "ui_audio",
        "z": "8f685eae.e4184",
        "name": "",
        "group": "b0a7fccd.a7aad",
        "voice": "",
        "always": false,
        "x": 700,
        "y": 440,
        "wires": []
    },
    {
        "id": "a1959f52.d3a478",
        "type": "change",
        "z": "8f685eae.e4184",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "filename",
                "pt": "msg",
                "to": "filename",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 350,
        "y": 440,
        "wires": [
            [
                "848a56ea.2f36b"
            ]
        ]
    },
    {
        "id": "39dd601f.30ef1",
        "type": "change",
        "z": "8f685eae.e4184",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "filename",
                "pt": "flow",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 340,
        "y": 340,
        "wires": [
            []
        ]
    },
    {
        "id": "ecbc10b3.b52c38",
        "type": "comment",
        "z": "8f685eae.e4184",
        "name": "Create a dashboard drop-down with list of SFX to choose from",
        "info": "",
        "x": 330,
        "y": 300,
        "wires": []
    },
    {
        "id": "fd0f700e.fd32b8",
        "type": "comment",
        "z": "8f685eae.e4184",
        "name": "When button pressed, retrieve selected SFX file and send to audio out",
        "info": "",
        "x": 350,
        "y": 400,
        "wires": []
    },
    {
        "id": "15fe206a.4a0578",
        "type": "ui_group",
        "z": "",
        "name": "Audio",
        "tab": "31696413.9a5234",
        "order": 2,
        "disp": true,
        "width": "6",
        "collapse": false
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
        "id": "31696413.9a5234",
        "type": "ui_tab",
        "z": "",
        "name": "Home",
        "icon": "dashboard",
        "order": 4,
        "disabled": false,
        "hidden": false
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