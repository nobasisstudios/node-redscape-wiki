It is common for escape rooms to feature a monitor in the room with a continuously updated display of the game time remaining, together with the ability to display hints or messages for the players. 
There are several steps to achieving this, as follows:

## Hide the title bar
To remove the top title bar, select the "Hide the title bar" option in the Node-RED dashboard options screen: 
![Dashboard settings](https://raw.githubusercontent.com/playfultechnology/propcontrol/master/Documentation/screenshots/roomdisplay_hide_titlebar.png)

## Create an alternative method of switching dashboard groups
The problem with having removed the top menu bar is that we have now lost the ability to switch between dashboard tabs. Fortunately, we can manually add in a button that switches from the editor view to the in-game view using a UI control node, as follows:
