This recipe demonstrates how to use Node-RED's uibuilder node to have a game timer and clue delivery text on top of a looping video background, and also have the ability to trigger a video overlay that appears on top.

## Flow

```[
    {
        "id": "7daa0e0f.94fa2",
        "type": "tab",
        "label": "Flow 1",
        "disabled": false,
        "info": ""
    },
    {
        "id": "e7d9299c.bdf7f",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Convert ms into hh:mm:ss",
        "info": "",
        "x": 670,
        "y": 160,
        "wires": []
    },
    {
        "id": "4582c4d0.5f7514",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Calculate time elapsed (or time remaining)",
        "info": "",
        "x": 360,
        "y": 160,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "ad0ff882.98e9e8",
        "type": "inject",
        "z": "7daa0e0f.94fa2",
        "name": "Every 1 sec",
        "props": [
            {
                "p": "payload"
            },
            {
                "p": "topic",
                "vt": "str"
            }
        ],
        "repeat": "1",
        "crontab": "",
        "once": true,
        "onceDelay": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "x": 110,
        "y": 200,
        "wires": [
            [
                "7c66bf35.7453b"
            ]
        ]
    },
    {
        "id": "c73acc96.e72f5",
        "type": "inject",
        "z": "7daa0e0f.94fa2",
        "name": "",
        "props": [
            {
                "p": "payload",
                "v": "Here's a clue!",
                "vt": "str"
            },
            {
                "p": "topic",
                "v": "",
                "vt": "string"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "",
        "payload": "Here's a clue!",
        "payloadType": "str",
        "x": 110,
        "y": 340,
        "wires": [
            [
                "b782529.f84303"
            ]
        ]
    },
    {
        "id": "b87de174.0904f",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Timer",
        "info": "",
        "x": 110,
        "y": 160,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "7c66bf35.7453b",
        "type": "function",
        "z": "7daa0e0f.94fa2",
        "name": "Update Game Timer",
        "func": "// The current timestamp is injected at the start of the flow\nvar currentTime = msg.payload;\n\n// If this is the first time the flow has run, set the start time\nif(flow.get(\"startTime\") === undefined) {\n    flow.set(\"startTime\", currentTime);\n}\nvar startTime = flow.get(\"startTime\");\n\n// Calculate the time elapsed since the flow started\nvar elapsedTime = (currentTime - startTime);\n\n// Update the msg to carry time elapsed      \nmsg.time = elapsedTime;\n// Or, time remaining\nmsg.time = 3600000 - elapsedTime;\n\n// Display value in the editor\nnode.status({fill:\"green\",shape:\"dot\",text:elapsedTime});\n\n// Pass value along the flow\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 360,
        "y": 200,
        "wires": [
            [
                "cdf5f82c.df5a6"
            ]
        ]
    },
    {
        "id": "cdf5f82c.df5a6",
        "type": "function",
        "z": "7daa0e0f.94fa2",
        "name": "Format Time As HH:MM:SS",
        "func": "// msg.time is in milliseconds\nvar t = msg.time / 1000;\nvar h = Math.floor(t / 3600);\nvar m = Math.floor(t % 3600 / 60);\nvar s = Math.floor(t % 3600 % 60);\n\n// Format into hh:mm:ss\nmsg.timerText = (\"0\" + h).slice(-2) + \":\" + (\"0\" + m).slice(-2) + \":\" + (\"0\" + s).slice(-2);\n\n// Update the editor node\nnode.status({fill:\"green\", shape:\"dot\", text:msg.timerText});\n\n// Forward the message along the flow\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 660,
        "y": 200,
        "wires": [
            [
                "b92a0984.a479c"
            ]
        ]
    },
    {
        "id": "b782529.f84303",
        "type": "trigger",
        "z": "7daa0e0f.94fa2",
        "name": "",
        "op1": "",
        "op2": " ",
        "op1type": "pay",
        "op2type": "str",
        "duration": "10",
        "extend": false,
        "units": "s",
        "reset": "",
        "bytopic": "all",
        "outputs": 1,
        "x": 370,
        "y": 340,
        "wires": [
            [
                "f93ed504.8ff1e8"
            ]
        ]
    },
    {
        "id": "f93ed504.8ff1e8",
        "type": "change",
        "z": "7daa0e0f.94fa2",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "clueText",
                "pt": "msg",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 690,
        "y": 340,
        "wires": [
            [
                "b92a0984.a479c"
            ]
        ]
    },
    {
        "id": "b92a0984.a479c",
        "type": "join",
        "z": "7daa0e0f.94fa2",
        "name": "",
        "mode": "custom",
        "build": "merged",
        "property": "",
        "propertyType": "full",
        "key": "topic",
        "joiner": "\\n",
        "joinerType": "str",
        "accumulate": true,
        "timeout": "",
        "count": "1",
        "reduceRight": false,
        "reduceExp": "",
        "reduceInit": "",
        "reduceInitType": "num",
        "reduceFixup": "",
        "x": 970,
        "y": 300,
        "wires": [
            [
                "f4c671fc.65fa3"
            ]
        ]
    },
    {
        "id": "be0ceb5e.052c2",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Duration to show clue before clearing",
        "info": "",
        "x": 390,
        "y": 300,
        "wires": []
    },
    {
        "id": "924ff0db.9eedf",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Combine time + clue + video",
        "info": "",
        "x": 1000,
        "y": 260,
        "wires": []
    },
    {
        "id": "a89fc7c6.46b55",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Send clue",
        "info": "",
        "x": 100,
        "y": 300,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "60685308.193364",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Set msg variable",
        "info": "",
        "x": 680,
        "y": 300,
        "wires": []
    },
    {
        "id": "493f2099.af2b9",
        "type": "inject",
        "z": "7daa0e0f.94fa2",
        "name": "Trigger",
        "props": [
            {
                "p": "payload"
            }
        ],
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "topic": "",
        "payload": "https://ia800701.us.archive.org/26/items/SampleVideo1280x7205mb/SampleVideo_1280x720_5mb.mp4",
        "payloadType": "str",
        "x": 110,
        "y": 480,
        "wires": [
            [
                "60c7763.d196288"
            ]
        ]
    },
    {
        "id": "60c7763.d196288",
        "type": "change",
        "z": "7daa0e0f.94fa2",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "videoOverlaySrc",
                "pt": "msg",
                "to": "payload",
                "tot": "msg"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 590,
        "y": 480,
        "wires": [
            [
                "b92a0984.a479c"
            ]
        ]
    },
    {
        "id": "f4c671fc.65fa3",
        "type": "uibuilder",
        "z": "7daa0e0f.94fa2",
        "name": "",
        "topic": "",
        "url": "room_display",
        "fwdInMessages": false,
        "allowScripts": false,
        "allowStyles": false,
        "copyIndex": true,
        "showfolder": false,
        "useSecurity": false,
        "sessionLength": 432000,
        "tokenAutoExtend": false,
        "x": 1260,
        "y": 300,
        "wires": [
            [],
            []
        ]
    },
    {
        "id": "be92a823.2ccd2",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Send to display",
        "info": "",
        "x": 1260,
        "y": 260,
        "wires": []
    },
    {
        "id": "68a4db1.8f677a4",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Send video overlay",
        "info": "",
        "x": 110,
        "y": 440,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "4e1f05d5.2caea4",
        "type": "comment",
        "z": "7daa0e0f.94fa2",
        "name": "Set msg variable",
        "info": "",
        "x": 600,
        "y": 440,
        "wires": []
    }
]
```
Then, edit the source files for the uibuilder node:

### index.html
```<!doctype html>
<html lang="en">
<head>
    <!-- Put your own custom styles in here -->
    <link rel="stylesheet" href="./index.css" media="all">
    <!-- Include Webfont to style text in custom font -->
    <link href="https://fonts.googleapis.com/css?family=Staatliches&display=swap" rel="stylesheet">
</head>
<body>
    <!-- The "app" element contains any content that receives dynamic updates -->
    <div id="app">
        <video autoplay muted loop class="background" id="backgroundVideo">
            <source src="/roomdisplay_bg.mp4" type="video/mp4" />
        </video>
        <video autoplay muted loop class="overlay" id="overlayVideo" ref="videoOverlay">
            <source  src="" type="video/mp4" />
        </video>
        <div class="timerText">{{msg.timerText}}</div>
        <div class="clueText">{{msg.clueText}}</div>
    </div>
    <!-- uibuilder script libraries -->
    <script src="../uibuilder/vendor/socket.io/socket.io.js"></script>
    <script src="../uibuilder/vendor/vue/dist/vue.min.js"></script>
    <script src="./uibuilderfe.min.js"></script>
    <!-- Put any additional custom code in here -->
    <script src="./index.js"></script>
</body>
</html>
```

### index.js
```
lastSrc="";

var app = new Vue({
    // The HTML element to attach to
	el: '#app',
    // Variables defined here will be avalable and updated within the HTML
	data: {
		msg: '[No Message Received Yet]',
	},
    // Callback function when Vue library is fully loaded
	mounted: function() {
	    // Start up uibuilder
		uibuilder.start();
		// Keep a reference to the Vue app
		var vueApp = this;
        // Callback triggered when node receives a (non-control) msg
		uibuilder.onChange('msg', function(msg) {
			vueApp.msg = msg;
			
			if(msg.videoOverlaySrc != lastSrc) {
		        vueApp.$refs.videoOverlay.src = msg.videoOverlaySrc;
		        lastSrc = msg.videoOverlaySrc;
			}
		});
			
	},
});
```

### index.css
```body {

    padding: 0;
    margin: 0;
    overflow: hidden;  /* Don't display any scroll bars */
}

.background, .backgroundVideo {
  position: fixed; 
  right: 0;
  bottom: 0;
  min-width: 100%;
  min-height: 100%;
  z-index: -10;
}

.overlay, .overlayVideo {
  position: fixed; 
  left: 200px;
  top: 100px;
  width: 1080px;
  height: 480px;
  z-index: 10;
}

#app {
    position: fixed;
    text-align: center;
    margin: auto;
    font-family: 'Staatliches', cursive;
    color: #999;
    right: 0;
    bottom: 0;
    min-width: 100%;
    min-height: 100%;
}
.timerText { 
    position: fixed;
    top:100px;
    left:400px;
    font-size: 100px;
    
}
.clueText {
    position: fixed;
    top:200px;
    left:400px;
    font-size: 100px;
}
```