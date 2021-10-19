This wiki contains "recipes" - individual snippets of code and instructions that demonstrate how to use Node-RED to automate various common escape room tasks. 

Recipes assume that you can access a running instance of Node-RED (for instructions, see <a href="https://nodered.org/docs/getting-started/local">this written guide</a> or <a href="https://www.youtube.com/watch?v=3jm2uED1wlg">this video</a>.

To use a recipe, download or copy the JSON code of the flow to the clipboard, and paste it into the <a href="https://nodered.org/docs/user-guide/editor/workspace/import-export">import dialog</a> box of your Node-RED instance.

IMPORTANT NOTE: There was a breaking change introduced in version v3.1.3 of the uibuilder node (released Jan 2021), which means that you now need to manually install the vue libraries which Node-RedScape uses. To do, double-click to edit the properties of the uibuilder node in a flow, and open the front-end library manager. Then, under "Installed Packages", click the add+ icon and type the name of the following packages if they are not already installed:
- bootstrap-vue
- bootstrap
- jquery
- vue