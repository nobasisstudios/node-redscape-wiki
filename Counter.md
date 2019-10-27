This function node creates a counter that stores how many times it has been called, displays that value in the editor, and also passes the value in the "counter" property of the msg object. This can be used in subsequent nodes in the flow to change behaviour depending on the number of times a particular action has occurred.
```
[
    {
        "id": "890fc630.1bc668",
        "type": "function",
        "z": "729e4378.2c89bc",
        "name": "Counter",
        "func": "// Retrieve any existing counter value from this node's context,\n// or initialise a new counter at 0 if it doesn't exist already\nvar counter = context.get('counter') || 0;\n\n// Increase the counter by one\ncounter += 1;\n\n// Store the new value back in the node's context\ncontext.set('counter', counter);\n\n// Add the counter to the outgoing msg object\nmsg.counter = counter;\n\n// Display counter value underneath the node in the editor\nnode.status({fill:\"grey\", shape:\"ring\", text: counter});\n\n// Pass the message object along the flow\nreturn msg;",
        "outputs": 1,
        "noerr": 0,
        "x": 443.5,
        "y": 445,
        "wires": [
            []
        ]
    }
]
```