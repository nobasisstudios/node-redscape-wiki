This is a recipe for a very common dual-purpose in-game ER display, which provides players with a countdown of their game time remaining together with the option to display text giving them a clue to solve the next puzzle.

![Example Game Timer + Clue Delivery Display](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/roomdisplay_example_2.jpg)

## Flow
* The top branch of the flow is responsible for the game timer. It is triggered by an inject node set to repeat every second, which sends the timestamp of the current time. The function node receives this value - if it is the first time the flow has run, that time is stored in the flow context; otherwise the existing stored start time is retrieved. By comparing the current time to the start time, you can either calculate the elapsed time or time remaining in the game. This is then formatted into HH:MM:SS format and stored as ``msg.timerText``.
* The lower branch sets the clue text. It is currently started by a manual inject node with a hard-coded clue, though this could easily be replaced with a button etc. The **trigger **node sends the clue text and then, after a specified amount of time (currently 10 secs), sends an empty string instead - this is to ensure on-screen clues remain relevant by only being displayed for a specified amount of time before disappearing. The string to be displayed - either clue or empty text - is stored in ``msg.clueText``. When the clue is sent, an audio SFX file is also sent to the audio output, creating an audible cue to let players know a clue has been sent.
* The two branches are combined using a **join** node to create a single msg object containing both the timer and clue information, and then passed forward to a **ui_builder node** for display.

![Game timer and clue display flow](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_cluetimer.png)

```
[
    {
        "id": "e7d9299c.bdf7f",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Convert ms into hh:mm:ss",
        "info": "",
        "x": 1690,
        "y": 140,
        "wires": []
    },
    {
        "id": "4582c4d0.5f7514",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Calculate time elapsed (or time remaining)",
        "info": "",
        "x": 1380,
        "y": 140,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "aaa89ca.c8d0ee",
        "type": "uibuilder",
        "z": "1451d340.f6e9e5",
        "name": "Game Timer and Clue Display",
        "topic": "",
        "url": "timer_clue_display",
        "fwdInMessages": false,
        "allowScripts": false,
        "allowStyles": false,
        "copyIndex": true,
        "showfolder": false,
        "x": 2230,
        "y": 280,
        "wires": [
            [],
            []
        ]
    },
    {
        "id": "ad0ff882.98e9e8",
        "type": "inject",
        "z": "1451d340.f6e9e5",
        "name": "Every 1 sec",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "1",
        "crontab": "",
        "once": true,
        "onceDelay": "",
        "x": 1110,
        "y": 180,
        "wires": [
            [
                "7c66bf35.7453b"
            ]
        ]
    },
    {
        "id": "c73acc96.e72f5",
        "type": "inject",
        "z": "1451d340.f6e9e5",
        "name": "",
        "topic": "",
        "payload": "Here's a clue!",
        "payloadType": "str",
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": 0.1,
        "x": 1110,
        "y": 380,
        "wires": [
            [
                "b782529.f84303",
                "4ef47aff.7c87b4"
            ]
        ]
    },
    {
        "id": "b87de174.0904f",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Trigger",
        "info": "",
        "x": 1130,
        "y": 140,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "433482f3.99a7b4",
        "type": "ui_audio",
        "z": "1451d340.f6e9e5",
        "name": "",
        "group": "9f69b1e6.70efd8",
        "voice": "",
        "always": true,
        "x": 1680,
        "y": 480,
        "wires": []
    },
    {
        "id": "4ef47aff.7c87b4",
        "type": "file in",
        "z": "1451d340.f6e9e5",
        "name": "",
        "filename": "C:\\Users\\alast\\Music\\sfx\\musicbox.mp3",
        "format": "",
        "chunk": false,
        "sendError": false,
        "encoding": "none",
        "x": 1428,
        "y": 480,
        "wires": [
            [
                "433482f3.99a7b4"
            ]
        ]
    },
    {
        "id": "7c66bf35.7453b",
        "type": "function",
        "z": "1451d340.f6e9e5",
        "name": "Update Game Timer",
        "func": "// The current timestamp is injected at the start of the flow\nvar currentTime = msg.payload;\n\n// If this is the first time the flow has run, set the start time\nif(flow.get(\"startTime\") === undefined) {\n    flow.set(\"startTime\", currentTime);\n}\nvar startTime = flow.get(\"startTime\");\n\n// Calculate the time elapsed since the flow started\nvar elapsedTime = (currentTime - startTime);\n\n// Update the msg to carry time elapsed      \nmsg.time = elapsedTime;\n// Or, time remaining\nmsg.time = 3600000 - elapsedTime;\n\n// Display value in the editor\nnode.status({fill:\"green\",shape:\"dot\",text:elapsedTime});\n\n// Pass value along the flow\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1380,
        "y": 180,
        "wires": [
            [
                "cdf5f82c.df5a6"
            ]
        ]
    },
    {
        "id": "cdf5f82c.df5a6",
        "type": "function",
        "z": "1451d340.f6e9e5",
        "name": "Format Time As HH:MM:SS",
        "func": "// msg.time is in milliseconds\nvar t = msg.time / 1000;\nvar h = Math.floor(t / 3600);\nvar m = Math.floor(t % 3600 / 60);\nvar s = Math.floor(t % 3600 % 60);\n\n// Format into hh:mm:ss\nmsg.timerText = (\"0\" + h).slice(-2) + \":\" + (\"0\" + m).slice(-2) + \":\" + (\"0\" + s).slice(-2);\n\n// Update the editor node\nnode.status({fill:\"green\", shape:\"dot\", text:msg.timerText});\n\n// Forward the message along the flow\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 1680,
        "y": 180,
        "wires": [
            [
                "b92a0984.a479c"
            ]
        ]
    },
    {
        "id": "4c21f08.a20a61",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Display on ui builder page",
        "info": "",
        "x": 2230,
        "y": 240,
        "wires": []
    },
    {
        "id": "b782529.f84303",
        "type": "trigger",
        "z": "1451d340.f6e9e5",
        "op1": "",
        "op2": " ",
        "op1type": "pay",
        "op2type": "str",
        "duration": "10",
        "extend": false,
        "units": "s",
        "reset": "",
        "bytopic": "all",
        "name": "",
        "x": 1390,
        "y": 380,
        "wires": [
            [
                "f93ed504.8ff1e8"
            ]
        ]
    },
    {
        "id": "f93ed504.8ff1e8",
        "type": "change",
        "z": "1451d340.f6e9e5",
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
        "x": 1710,
        "y": 380,
        "wires": [
            [
                "b92a0984.a479c"
            ]
        ]
    },
    {
        "id": "b92a0984.a479c",
        "type": "join",
        "z": "1451d340.f6e9e5",
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
        "x": 1990,
        "y": 280,
        "wires": [
            [
                "aaa89ca.c8d0ee"
            ]
        ]
    },
    {
        "id": "be0ceb5e.052c2",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "How long to display clue before clearing",
        "info": "",
        "x": 1410,
        "y": 340,
        "wires": []
    },
    {
        "id": "924ff0db.9eedf",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Combine time + clue",
        "info": "",
        "x": 1990,
        "y": 240,
        "wires": []
    },
    {
        "id": "a89fc7c6.46b55",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Set clue text",
        "info": "",
        "x": 1110,
        "y": 340,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "60685308.193364",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Set msg variable",
        "info": "",
        "x": 1700,
        "y": 340,
        "wires": []
    },
    {
        "id": "4d316d9.5ee8c94",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Select SFX to play when clue displayed",
        "info": "",
        "x": 1430,
        "y": 440,
        "wires": []
    },
    {
        "id": "bbfae306.01e8b",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Send to audio output",
        "info": "",
        "x": 1720,
        "y": 440,
        "wires": []
    },
    {
        "id": "9f69b1e6.70efd8",
        "type": "ui_group",
        "z": "",
        "name": "Main",
        "tab": "31696413.9a5234",
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
        "order": 3,
        "disabled": false,
        "hidden": false
    }
]
```

## UI builder source files
The following code listings show the index.html|.css|.js files required to display the timer and clue text in the format as shown in the screenshot above, on top of a static image background.
### index.html
```
<!doctype html>
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
### index.css
```
body {
    padding: 0;
    margin: 0;
    overflow: hidden; /* Don't display any scroll bars */
}
#app {
    background: url("http://eskipaper.com/images/blue-abstract-background-2.jpg");
    height: 1080px;
    width: 100%;
    text-align: center;
    margin: auto;
    font-family: 'Staatliches', cursive;
    color: #FFF;
}
.timerText { 
    font-size: 300px;
    
}
.clueText {
    top:100px;
    font-size: 100px;
}
```
### index.js
```
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
		});
	},
});
```