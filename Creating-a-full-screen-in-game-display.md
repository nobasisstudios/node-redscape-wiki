It is common for escape rooms to feature a monitor with a continuously updated countdown of the game time remaining, together with the ability to display hints or messages for the players. There are also many other situations where you might want to be able to display content on a screen in the room, such as triggering a video or image to appear at certain points of gameplay.

This recipe will illustrate how to create flows that expose an HTTP endpoint (e.g. http://localhost:1880/display1, http://localhost:1880/display2), and send text, full-screen image or video content to any of those displays.
![Full-Screen Room Display](https://github.com/playfultechnology/node-redscape/blob/master/Documentation/screenshots/roomdisplay_example.jpg)

There are several steps to achieving this, as follows:

## Install ui_builder
Node-RED interfaces are commonly created using the node-red dashboard, which makes it easy to create user interfaces containing multiple tabs, featuring buttons, sliders, and many other controls. However, for a simple, full-screen display that doesn't include all that extra menu navigation, we'll used an alternative UI module instead - 
node-red-contrib-uibuilder. Install it from the Manage Palette menu, as shown:

![Installing the ui builder node](https://github.com/playfultechnology/propcontrol/blob/master/Documentation/screenshots/roomdisplay_install_uibuilder.png)

## Edit settings.js to enable Node-RED to serve static content
By default, web applications like Node-RED aren't allowed to access files on your local computer's file system. If we want to place images or videos on our full screen display, we'll need to specify a particular folder in which they have to be placed, and give Node-RED permission to use that folder. To do so, edit the settings.js file, which can be found in the ~/.node-red directory (on Windows, this will be C:\users\username\.node-red\settings.js).
On line 109, uncomment the reference to httpStatic, and edit to point to an appropriate local folder, e.g. 
```
    httpStatic: 'C://Users/username/.node-red/static',
```
After restarting Node-RED, you will then be able to access an image file saved in ``/static/x.png`` by going to ``http://localhost:1880/x.png`` 

## Create a flow
Having installed ui_builder, you will have a new node type available in the palette. Drag this onto the flow.

![ui builder node](https://github.com/playfultechnology/propcontrol/blob/master/Documentation/screenshots/node_uibuilder.png)

Double-click on the node to bring up the properties panel.
* The **url** will be the address on the local webserver at which this page will be displayed in the browser. For example, setting the url value of "display1" will mean that the display created will be available at http://localhost:1880/display1
* Optionally, set the **Name** that this node will be labelled in the editor
* Then, click the **Edit Source Files** button

![ui builder node properties](https://github.com/playfultechnology/propcontrol/blob/master/Documentation/screenshots/node_uibuilder_properties.png)

## Edit the HTML/CSS/JS source files for the page
The uibuilder node serves up a simple webpage based on three core files - index.html (page content), index.css (styling), and index.js (functionality). When you drag a new uibuilder node into the editor, these will be pre-populated with some example content. Load each file in turn using the drop-down menu and replace them with code from below depending on the purpose of the display (clicking "Save" after editing each file).  
 ![ui builder node source files](https://github.com/playfultechnology/propcontrol/blob/master/Documentation/screenshots/node_uibuilder_sourcefiles.png)

## Deploy and Test
Having edited the web files for the content in question, deploy the flow as usual, and then point your webbrowser to the page specified in the **url** value of the ui_builder node.
To make the page content fill the screen, you should enable the full-screen mode in your browser (normally by pressing F11). 