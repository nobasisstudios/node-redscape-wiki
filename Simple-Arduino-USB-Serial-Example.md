Perhaps the easiest way to connect an Arduino to Node-RED is using the USB cable which is also used to supply power and upload sketches from the Arduino IDE. When you call the Serial.print() function in Arduino code, it transmits data using this serial connection, which is commonly printed using the Arduino Serial Monitor. However, it can just as easily be received by a Node-RED **Serial In** node, and the value used in a flow, as in this example.

![Simple USB Serial flow](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_simple_serial.png)

For a full demonstration and instructions, see the following video:
https://www.youtube.com/watch?v=ryEFNy1E_LU

```
[
    {
        "id": "b93551ea.4cc138",
        "type": "serial in",
        "z": "efe84a08.84799",
        "name": "",
        "serial": "3c58cfe0.eb2968",
        "x": 220,
        "y": 140,
        "wires": [
            [
                "ada0bca1.5ec988"
            ]
        ]
    },
    {
        "id": "ada0bca1.5ec988",
        "type": "ui_text",
        "z": "efe84a08.84799",
        "group": "d33a175b.73e43",
        "order": 0,
        "width": 0,
        "height": 0,
        "name": "",
        "label": "Reed Switch",
        "format": "{{msg.payload}}",
        "layout": "row-spread",
        "x": 410,
        "y": 140,
        "wires": []
    },
    {
        "id": "3c58cfe0.eb2968",
        "type": "serial-port",
        "z": "",
        "serialport": "COM5",
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
        "id": "d33a175b.73e43",
        "type": "ui_group",
        "z": "",
        "name": "Default",
        "tab": "3cb881e0.05b746",
        "disp": true,
        "width": "6",
        "collapse": false
    },
    {
        "id": "3cb881e0.05b746",
        "type": "ui_tab",
        "z": "",
        "name": "Home",
        "icon": "dashboard",
        "disabled": false,
        "hidden": false
    }
]
```