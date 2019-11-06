[DMX](https://en.wikipedia.org/wiki/DMX512) is a standard interface commonly used to control stage lighting and effects. This recipe demonstrates how you can create a Node-RED dashboard interface that acts as a "virtual lighting desk" - enabling you to set ambient lights using a colour wheel and brightness slider, save and restore preset levels, enable individual spotlights via toggles, or trigger special effects such as lighting, UV blackout, or atmospheric effects such as police sirens.

![DMX Virtual Lighting Desk on Node-RED Dashboard](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/dashboard_dmx.png)
![DMX Flow](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_dmx.png)

## Flow
The following flow can be imported into Node-RED to demonstrate a range of different DMX effects possible, as demonstrated in [this video](https://www.youtube.com/watch?v=nPNFbN80pSg&feature=youtu.be). The different flows construct a JSON payload which specifies channel:value pairs of which effects to trigger, which is sent to a Serial Out node connected to the Arduino. The Arduino is running a [sketch](https://github.com/playfultechnology/node-redscape/tree/master/Arduino/DMX) which reads and parses this JSON and controls the DMX devices accordingly. 
Note that if you have a different DMX interface you will simply need to send the flow output to a different node in place of the Serial Out. Also note that you will need to modify the DMX channels set in each node to correspond to the channels used by your set of DMX slave devices (there is no "standard" DMX mapping that Ch1 = Brightness, Ch2 = Red etc. - these differ between every device!).

### Prequisites
If you don't already have them installed, you will need to add the following modules to your Node-RED palette by going to the _Manage Palette_ menu:
* https://flows.nodered.org/node/node-red-node-serialport
* https://flows.nodered.org/node/node-red-dashboard
 
```
[
    {
        "id": "462733d4.14dab4",
        "type": "ui_colour_picker",
        "z": "8ccf5ec1.fe5818",
        "name": "Colour Wheel UI",
        "label": "Colour",
        "group": "e6b1dbdf.98de7",
        "format": "rgb",
        "outformat": "object",
        "showSwatch": false,
        "showPicker": true,
        "showValue": false,
        "showHue": false,
        "showAlpha": false,
        "showLightness": false,
        "square": "false",
        "dynOutput": "true",
        "order": 2,
        "width": 0,
        "height": 0,
        "passthru": true,
        "topic": "",
        "x": 520,
        "y": 400,
        "wires": [
            [
                "bdf8e882.f8d7b8"
            ]
        ]
    },
    {
        "id": "32cb2ba9.340cd4",
        "type": "serial out",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "serial": "5401478c.59691",
        "x": 1350,
        "y": 460,
        "wires": []
    },
    {
        "id": "933e3562.a5c09",
        "type": "function",
        "z": "8ccf5ec1.fe5818",
        "name": "Assign RGB to DMX channels 2/3/4",
        "func": "msg.payload[\"channels\"] = [\n    {\n    \"channel\" : \"2\",\n    \"value\" : msg.payload.r\n    },\n    {\n    \"channel\" : \"3\",\n    \"value\" : msg.payload.g\n    },\n    {\n    \"channel\" : \"4\",\n    \"value\" : msg.payload.b\n    }\n];\n\n// Update the editor node\nnode.status({fill:\"grey\", shape:\"ring\", text: \"RGB(\" + msg.payload.r + \",\" + msg.payload.g + \",\" + msg.payload.b + \")\"});\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1000,
        "y": 400,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "f70fe8af.88a7b",
        "type": "ui_slider",
        "z": "8ccf5ec1.fe5818",
        "name": "Brightness Slider UI",
        "label": "Brightness",
        "tooltip": "",
        "group": "e6b1dbdf.98de7",
        "order": 1,
        "width": 0,
        "height": 0,
        "passthru": true,
        "outs": "all",
        "topic": "",
        "min": 0,
        "max": "255",
        "step": 1,
        "x": 510,
        "y": 280,
        "wires": [
            [
                "cc6befdc.1baf48"
            ]
        ]
    },
    {
        "id": "f713d37f.41648",
        "type": "function",
        "z": "8ccf5ec1.fe5818",
        "name": "Assign to DMX channel 1",
        "func": "var brightness = msg.payload;\n\nmsg.payload = {\n    \"channels\": [\n        {\n            \"channel\": 1,\n            \"value\": brightness\n        }\n    ]\n}\n\n// Update the editor node\nnode.status({fill:\"grey\", shape:\"ring\", text: \"Brightness:\" + brightness});\n\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 970,
        "y": 280,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "e5208055.3cdba",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "Assign initial brightness",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "200",
                "tot": "num"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 270,
        "y": 280,
        "wires": [
            [
                "f70fe8af.88a7b"
            ]
        ]
    },
    {
        "id": "4a40a869.85f198",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "Set initial RGB value",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "#80808080",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 260,
        "y": 400,
        "wires": [
            [
                "462733d4.14dab4"
            ]
        ]
    },
    {
        "id": "3436fcbd.be6144",
        "type": "trigger",
        "z": "8ccf5ec1.fe5818",
        "op1": "true",
        "op2": "false",
        "op1type": "bool",
        "op2type": "bool",
        "duration": "5",
        "extend": false,
        "units": "s",
        "reset": "",
        "bytopic": "all",
        "name": "",
        "x": 700,
        "y": 520,
        "wires": [
            [
                "ad8d175b.67b34"
            ]
        ]
    },
    {
        "id": "a50ec4b3.f2f0c",
        "type": "ui_button",
        "z": "8ccf5ec1.fe5818",
        "name": "UV Blackout",
        "group": "e6b1dbdf.98de7",
        "order": 6,
        "width": 0,
        "height": 0,
        "passthru": false,
        "label": "UV Blackout",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "",
        "payloadType": "str",
        "topic": "",
        "x": 530,
        "y": 520,
        "wires": [
            [
                "3436fcbd.be6144",
                "cdde4e5e.208b2"
            ]
        ]
    },
    {
        "id": "9f0904ae.fa7798",
        "type": "inject",
        "z": "8ccf5ec1.fe5818",
        "name": "Delayed Startup",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": true,
        "onceDelay": "5",
        "x": 240,
        "y": 80,
        "wires": [
            [
                "ce39bf06.e19488"
            ]
        ]
    },
    {
        "id": "cc6befdc.1baf48",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "brightness",
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
        "x": 730,
        "y": 280,
        "wires": [
            [
                "f713d37f.41648"
            ]
        ]
    },
    {
        "id": "4dcf1ce9.8603d4",
        "type": "ui_button",
        "z": "8ccf5ec1.fe5818",
        "name": "Save",
        "group": "e6b1dbdf.98de7",
        "order": 3,
        "width": "3",
        "height": "1",
        "passthru": false,
        "label": "Save",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "",
        "payloadType": "str",
        "topic": "",
        "x": 270,
        "y": 120,
        "wires": [
            [
                "70575038.9552b8"
            ]
        ]
    },
    {
        "id": "f791912e.6f6f9",
        "type": "ui_button",
        "z": "8ccf5ec1.fe5818",
        "name": "Restore",
        "group": "e6b1dbdf.98de7",
        "order": 4,
        "width": "3",
        "height": "1",
        "passthru": false,
        "label": "Restore",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "",
        "payloadType": "str",
        "topic": "",
        "x": 260,
        "y": 160,
        "wires": [
            [
                "f79cc6c3.88f44"
            ]
        ]
    },
    {
        "id": "bdf8e882.f8d7b8",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "colour",
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
        "x": 720,
        "y": 400,
        "wires": [
            [
                "933e3562.a5c09"
            ]
        ]
    },
    {
        "id": "70575038.9552b8",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "Store brightness and RGB values",
        "rules": [
            {
                "t": "set",
                "p": "savedBrightness",
                "pt": "flow",
                "to": "brightness",
                "tot": "flow"
            },
            {
                "t": "set",
                "p": "savedColour",
                "pt": "flow",
                "to": "colour",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 500,
        "y": 120,
        "wires": [
            []
        ]
    },
    {
        "id": "fe54b9b6.5426c",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "Restore saved brightness",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "savedBrightness",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 270,
        "y": 320,
        "wires": [
            [
                "f70fe8af.88a7b"
            ]
        ]
    },
    {
        "id": "cff5dbe2.e74e8",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "Restore saved RGB",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "savedColour",
                "tot": "flow"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 260,
        "y": 440,
        "wires": [
            [
                "462733d4.14dab4"
            ]
        ]
    },
    {
        "id": "ce39bf06.e19488",
        "type": "link out",
        "z": "8ccf5ec1.fe5818",
        "name": "Delayed Start",
        "links": [
            "ba9a812c.a74f",
            "30c5774c.69271"
        ],
        "x": 375,
        "y": 80,
        "wires": []
    },
    {
        "id": "f79cc6c3.88f44",
        "type": "link out",
        "z": "8ccf5ec1.fe5818",
        "name": "Restore",
        "links": [
            "286d20d4.9afce",
            "849a4296.da07d"
        ],
        "x": 375,
        "y": 160,
        "wires": []
    },
    {
        "id": "ba9a812c.a74f",
        "type": "link in",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "links": [
            "ce39bf06.e19488"
        ],
        "x": 115,
        "y": 400,
        "wires": [
            [
                "4a40a869.85f198"
            ]
        ]
    },
    {
        "id": "30c5774c.69271",
        "type": "link in",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "links": [
            "ce39bf06.e19488"
        ],
        "x": 115,
        "y": 280,
        "wires": [
            [
                "e5208055.3cdba"
            ]
        ]
    },
    {
        "id": "286d20d4.9afce",
        "type": "link in",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "links": [
            "f79cc6c3.88f44"
        ],
        "x": 115,
        "y": 320,
        "wires": [
            [
                "fe54b9b6.5426c"
            ]
        ]
    },
    {
        "id": "849a4296.da07d",
        "type": "link in",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "links": [
            "f79cc6c3.88f44"
        ],
        "x": 115,
        "y": 440,
        "wires": [
            [
                "cff5dbe2.e74e8"
            ]
        ]
    },
    {
        "id": "7625f4ec.0a84ec",
        "type": "ui_button",
        "z": "8ccf5ec1.fe5818",
        "name": "Lightning Strike",
        "group": "e6b1dbdf.98de7",
        "order": 6,
        "width": 0,
        "height": 0,
        "passthru": false,
        "label": "Lightning Strike",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "",
        "payloadType": "str",
        "topic": "",
        "x": 520,
        "y": 660,
        "wires": [
            [
                "d0f99cc4.c9ac2",
                "a86c4f94.e8a72"
            ]
        ]
    },
    {
        "id": "ad8d175b.67b34",
        "type": "switch",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "true",
                "vt": "jsonata"
            },
            {
                "t": "eq",
                "v": "false",
                "vt": "jsonata"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 850,
        "y": 520,
        "wires": [
            [
                "432caa9a.cd7c0c"
            ],
            [
                "a476c2af.b5991"
            ]
        ]
    },
    {
        "id": "432caa9a.cd7c0c",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "Turn on UV flood",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "{\"channels\":[{\"channel\":1,\"value\":0},{\"channel\":11,\"value\":255},{\"channel\":12,\"value\":255},{\"channel\":13,\"value\":255},{\"channel\":14,\"value\":255}]}",
                "tot": "json"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1030,
        "y": 500,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "a476c2af.b5991",
        "type": "function",
        "z": "8ccf5ec1.fe5818",
        "name": "Restore previous lights",
        "func": "msg.payload = {\n    \"channels\": [\n        {\n            \"channel\": 1,\n            \"value\": flow.get(\"brightness\")\n        },\n        {\n            \"channel\": 8,\n            \"value\": 0\n        },\n        {\n            \"channel\": 9,\n            \"value\": 0\n        },\n        {\n            \"channel\": 11,\n            \"value\": 0\n        }\n    ]\n}\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1050,
        "y": 540,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "d0f99cc4.c9ac2",
        "type": "file in",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "filename": "C:\\Users\\alast\\Music\\SFX\\Thunder_Clap.wav",
        "format": "",
        "chunk": false,
        "sendError": false,
        "encoding": "none",
        "x": 810,
        "y": 700,
        "wires": [
            [
                "70581233.ac2a34"
            ]
        ]
    },
    {
        "id": "70581233.ac2a34",
        "type": "ui_audio",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "group": "e6b1dbdf.98de7",
        "voice": "",
        "always": "",
        "x": 1080,
        "y": 700,
        "wires": []
    },
    {
        "id": "a86c4f94.e8a72",
        "type": "function",
        "z": "8ccf5ec1.fe5818",
        "name": "Send Lightning Flashes",
        "func": "// Send a series of flashes of varying brightness as follows\nvar lightningIntensities = [32,128,164,128,96,64,128,64,32,0,255,255,192,176,164,148,128,112,96,64,56,32,24,16,8,4,2,0,0,0,0,0,0,0,0,0,0,0];\nfor(var i=0; i<lightningIntensities.length; i++){\n    msg.payload = {\n    \"channels\": [\n        {\n            \"channel\": 1,\n            \"value\": lightningIntensities[i]\n        },\n        {\n            \"channel\": 2,\n            \"value\": 224\n        },\n        {\n            \"channel\": 3,\n            \"value\": 224\n        },\n        {\n            \"channel\": 4,\n            \"value\": 255\n        }\n    ]};\n  node.send(msg);  \n}\n\n// Now restore the brightess and RGB value\nvar previousColour = flow.get(\"colour\");\nvar previousBrightness = flow.get(\"brightness\");\nmsg.payload = {    \n    \"channels\": [            \n        {            \n            \"channel\": 1,           \n            \"value\": previousBrightness\n        },\n        {            \n            \"channel\": 2,           \n            \"value\": previousColour.r\n        },\n        {            \n            \"channel\": 3,           \n            \"value\": previousColour.g\n        },\n        {            \n            \"channel\": 4,           \n            \"value\": previousColour.b\n        },\n    ]\n};\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1030,
        "y": 660,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "59231ba2.fc240c",
        "type": "ui_button",
        "z": "8ccf5ec1.fe5818",
        "name": "Police Siren",
        "group": "e6b1dbdf.98de7",
        "order": 6,
        "width": 0,
        "height": 0,
        "passthru": false,
        "label": "Police Siren",
        "tooltip": "",
        "color": "",
        "bgcolor": "",
        "icon": "",
        "payload": "",
        "payloadType": "str",
        "topic": "",
        "x": 510,
        "y": 800,
        "wires": [
            [
                "f04eb13e.3e3d28",
                "9ec18778.481a68"
            ]
        ]
    },
    {
        "id": "f04eb13e.3e3d28",
        "type": "file in",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "filename": "C:\\Users\\alast\\Music\\SFX\\police_siren.mp3",
        "format": "",
        "chunk": false,
        "sendError": false,
        "encoding": "none",
        "x": 810,
        "y": 840,
        "wires": [
            [
                "a9417c0b.f05c08"
            ]
        ]
    },
    {
        "id": "a9417c0b.f05c08",
        "type": "ui_audio",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "group": "e6b1dbdf.98de7",
        "voice": "",
        "always": "",
        "x": 1080,
        "y": 840,
        "wires": []
    },
    {
        "id": "9ec18778.481a68",
        "type": "function",
        "z": "8ccf5ec1.fe5818",
        "name": "Red/Blue Lights",
        "func": "for(var i=0; i<16; i++){\n    msg.payload = {\n    \"channels\": [\n        {\n            \"channel\": 1,\n            \"value\": 255\n        },\n        {\n            \"channel\": 2,\n            \"value\": (i%2 ? 255:0)\n        },\n        {\n            \"channel\": 3,\n            \"value\": 0\n        },\n        {\n            \"channel\": 4,\n            \"value\": (i%2 ? 0:255)\n        }\n    ]};\n  node.send(msg);  \n}\n\n// Now restore the brightess and RGB value\nvar previousColour = flow.get(\"colour\");\nvar previousBrightness = flow.get(\"brightness\");\nmsg.payload = {    \n    \"channels\": [            \n        {            \n            \"channel\": 1,           \n            \"value\": previousBrightness\n        },\n        {            \n            \"channel\": 2,           \n            \"value\": previousColour.r\n        },\n        {            \n            \"channel\": 3,           \n            \"value\": previousColour.g\n        },\n        {            \n            \"channel\": 4,           \n            \"value\": previousColour.b\n        },\n    ]\n};\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 820,
        "y": 800,
        "wires": [
            [
                "d70271ac.a3507"
            ]
        ]
    },
    {
        "id": "d70271ac.a3507",
        "type": "delay",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "pauseType": "rate",
        "timeout": "5",
        "timeoutUnits": "seconds",
        "rate": "2",
        "nbRateUnits": "1",
        "rateUnits": "second",
        "randomFirst": "1",
        "randomLast": "5",
        "randomUnits": "seconds",
        "drop": false,
        "x": 1070,
        "y": 800,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "cdde4e5e.208b2",
        "type": "file in",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "filename": "C:\\Users\\alast\\Music\\SFX\\uv_appear.wav",
        "format": "",
        "chunk": false,
        "sendError": false,
        "encoding": "none",
        "x": 800,
        "y": 580,
        "wires": [
            [
                "32cdcefd.64cf0a"
            ]
        ]
    },
    {
        "id": "32cdcefd.64cf0a",
        "type": "ui_audio",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "group": "e6b1dbdf.98de7",
        "voice": "",
        "always": "",
        "x": 1080,
        "y": 580,
        "wires": []
    },
    {
        "id": "7b9eebf7.89db04",
        "type": "ui_switch",
        "z": "8ccf5ec1.fe5818",
        "name": "Spotlight",
        "label": "Spotlight",
        "tooltip": "",
        "group": "e6b1dbdf.98de7",
        "order": 11,
        "width": 0,
        "height": 0,
        "passthru": true,
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
        "x": 760,
        "y": 960,
        "wires": [
            [
                "2d2fd4a0.0ce1a4"
            ]
        ]
    },
    {
        "id": "2d2fd4a0.0ce1a4",
        "type": "switch",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "property": "payload",
        "propertyType": "msg",
        "rules": [
            {
                "t": "eq",
                "v": "true",
                "vt": "jsonata"
            },
            {
                "t": "eq",
                "v": "false",
                "vt": "jsonata"
            }
        ],
        "checkall": "true",
        "repair": false,
        "outputs": 2,
        "x": 900,
        "y": 960,
        "wires": [
            [
                "c8314af0.d3ed6"
            ],
            [
                "b80d178e.5c20d"
            ]
        ]
    },
    {
        "id": "c8314af0.d3ed6",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "{\"channels\":[{\"channel\":15,\"value\":255},{\"channel\":16,\"value\":0},{\"channel\":17,\"value\":0},{\"channel\":18,\"value\":0},{\"channel\":19,\"value\":32}]}",
                "tot": "json"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1060,
        "y": 940,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "b80d178e.5c20d",
        "type": "change",
        "z": "8ccf5ec1.fe5818",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "payload",
                "pt": "msg",
                "to": "{\"channels\":[{\"channel\":15,\"value\":0}]}",
                "tot": "json"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1060,
        "y": 980,
        "wires": [
            [
                "32cb2ba9.340cd4"
            ]
        ]
    },
    {
        "id": "e6b1dbdf.98de7",
        "type": "ui_group",
        "z": "",
        "name": "Sound and Lighting",
        "tab": "d3a7f263.5bdd4",
        "order": 4,
        "disp": true,
        "width": "6",
        "collapse": false
    },
    {
        "id": "5401478c.59691",
        "type": "serial-port",
        "z": "",
        "serialport": "COM4",
        "serialbaud": "9600",
        "databits": "8",
        "parity": "none",
        "stopbits": "1",
        "waitfor": "",
        "newline": "\\n",
        "bin": "false",
        "out": "char",
        "addchar": "",
        "responsetimeout": "10000"
    },
    {
        "id": "d3a7f263.5bdd4",
        "type": "ui_tab",
        "z": "",
        "name": "Lighting",
        "icon": "dashboard",
        "disabled": false,
        "hidden": false
    }
]
```

## Arduino Code
The Arduino sketch used to accompany the above flow can be downloaded from https://github.com/playfultechnology/node-redscape/tree/master/Arduino/DMX