This example illustrates how to trigger different videos to be displayed on a page created using the **ui_builder** node. This is done by dynamically injecting different values for the video "src" attribute, which can be relative links to video files saved in the _httpStatic_ folder of the local Node-RED server, or http links to videos on the web. 

## Flow
Start by importing the following flow:
![Triggering different videos ](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_triggered_video.png)

```
[
    {
        "id": "e7d9299c.bdf7f",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Create UI ",
        "info": "The ui builder node is used to create a webpage that displays the chosen video, styled using CSS to fill the page. You can access the page at [/video_display](/video_display).\n\n## Configuration\nUpdate the files:\n\n* `index.html`\n* `index.js`\n* `index.css`\n\nWith content described at https://github.com/playfultechnology/node-redscape/wiki/Creating-a-full-screen-in-game-display",
        "x": 1611,
        "y": 356,
        "wires": []
    },
    {
        "id": "4582c4d0.5f7514",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Set video to play",
        "info": "Set the address of the source video. This can either be served from a website (e.g. http(s)://), or from a relative URL on the local Node-RED instance (in which case, static HTTP must be allowed in the settings.js file)",
        "x": 1382,
        "y": 316,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    },
    {
        "id": "aaa89ca.c8d0ee",
        "type": "uibuilder",
        "z": "1451d340.f6e9e5",
        "name": "Video Display triggered by source payload",
        "topic": "",
        "url": "video_display",
        "fwdInMessages": false,
        "allowScripts": false,
        "allowStyles": false,
        "copyIndex": true,
        "showfolder": false,
        "x": 1620,
        "y": 400,
        "wires": [
            [],
            []
        ]
    },
    {
        "id": "ad0ff882.98e9e8",
        "type": "inject",
        "z": "1451d340.f6e9e5",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": "",
        "x": 1200,
        "y": 360,
        "wires": [
            [
                "d4b7b19f.fd9ea"
            ]
        ]
    },
    {
        "id": "d4b7b19f.fd9ea",
        "type": "change",
        "z": "1451d340.f6e9e5",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "videoSrc",
                "pt": "msg",
                "to": "http://iandevlin.github.io/mdn/video-player/video/tears-of-steel-battle-clip-medium.mp4",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1390,
        "y": 360,
        "wires": [
            [
                "aaa89ca.c8d0ee"
            ]
        ]
    },
    {
        "id": "ba2b43f9.d14208",
        "type": "change",
        "z": "1451d340.f6e9e5",
        "name": "",
        "rules": [
            {
                "t": "set",
                "p": "videoSrc",
                "pt": "msg",
                "to": "/roomdisplay_bg.mp4",
                "tot": "str"
            }
        ],
        "action": "",
        "property": "",
        "from": "",
        "to": "",
        "reg": false,
        "x": 1390,
        "y": 440,
        "wires": [
            [
                "aaa89ca.c8d0ee"
            ]
        ]
    },
    {
        "id": "443d0bc8.7f4254",
        "type": "inject",
        "z": "1451d340.f6e9e5",
        "name": "",
        "topic": "",
        "payload": "",
        "payloadType": "date",
        "repeat": "",
        "crontab": "",
        "once": false,
        "onceDelay": "1",
        "x": 1200,
        "y": 440,
        "wires": [
            [
                "ba2b43f9.d14208"
            ]
        ]
    },
    {
        "id": "b87de174.0904f",
        "type": "comment",
        "z": "1451d340.f6e9e5",
        "name": "Trigger",
        "info": "",
        "x": 1188,
        "y": 316,
        "wires": [],
        "icon": "node-red/parser-html.svg"
    }
]
```
## Editing the source files
Double-click to edit the ui builder node and replace the contents of the index.html|.css|.js files as follows:
### index.html
```
<!doctype html>
<html lang="en">
<head>
    <!-- Put your own custom styles in here -->
    <link rel="stylesheet" href="./index.css" media="all"></head>
<body>
    <!-- The "app" element contains any content that receives dynamic updates -->
    <div id="app">
        <div class="outer-container">
            <div class="inner-container">
                <div class="background">
                    <video ref="videoRef" src="" id="video-player" loop="true" muted="muted" autoplay></video>
                </div>
            </div>
        </div>
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
.outer-container {
    width: 100%;
    height: 100%;
    text-align: center;
}
.inner-container {
    display: inline-block;
    position: relative;
}
.background {
    width: 100%;
    height: 100%;
}
```
### index.js
```
var app = new Vue({
    // Define the HTML element to attach the Vue object to
    el: '#app',
    // Callback function when Vue library is fully loaded
	mounted: function() {
	    // Start up uibuilder
		uibuilder.start();
		// Keep a reference to the Vue app
		var vueApp = this;
        // Callback triggered when node receives a (non-control) msg
		uibuilder.onChange('msg', function(msg) {
		    vueApp.$refs.videoRef.src = msg.videoSrc;
		});
	},
});
```