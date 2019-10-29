This example illustrates how to trigger different videos to be displayed on a page created using the **ui_builder** node. This is done by dynamically injecting different values for the video "src" attribute, which can be relative links to video files saved in the _httpStatic_ folder of the local Node-RED server, or http links to videos on the web. 

![Triggering different videos ](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/flow_triggered_video.png)

## index.html
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
## index.css
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
## index.js
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