# Developing Plugins

Plugins are modular extensions to the schema that add or modify the gameplay of your gamemode. While it is possible to write all your code within the schema folder, using plugins is recommended as it allows you to easily add, modify and/or remove organized code.

There are 2 ways to create a plugin in NutScript:

1. Single-file plugins, for small plugins such as [Permakill](https://github.com/NutScript/NutScript/blob/1.2/plugins/permakill.lua).
2. Folder based plugins, for bigger plugins that use items, languages, entities or derma, such as [Vendor](https://github.com/NutScript/NutScript/tree/1.2/plugins/vendor).

### **Single File Plugins**

1. Create a new ```.lua``` file in the plugins folder. Name the file whatever the plugin's name is. (e.g. ```permakill.lua```).

### **Folder Based Plugins**

1. Create a new folder in the plugins folder. Name the folder whatever the plugin's name is. (e.g. ```vendor```).
2. Create a new file within the plugin folder. Name it ```sh_plugin.lua```.

## **Starting**

In your plugin/```sh_plugin.lua``` file, include the following:

```lua
PLUGIN.name = "Plugin Name"
PLUGIN.author = "You, the Creator"
PLUGIN.desc = "A simple plugin that does something."
```

Here you'll need to set your plugin's name, the author, and a short description of what it does.

## **Hooking**

Normally, to hook a function, you'll need to use the following syntax:
```lua
hook.Add("Think", "myUniqueFunction", function()
    -- Do stuff
end)
```

However, NutScript has a special syntax for hooking functions within plugins.

```lua
function PLUGIN:Think()
    -- Do stuff
end
```

### **Why PLUGIN?**

The PLUGIN prefix allows us to tell NutScript that we are extending the functions we are going to use, rather than completely replace them with the ones we set inside the file. This prevents code from being removed from execution and therefore breaking some aspects of the gamemode or other plugins.

Advantages of using PLUGIN:

1. It gives the hooked code priority when executed. Meaning that code added via PLUGIN will run before most code added by addons.

2. It allows you to use code defined by PLUGIN to be run elsewhere in the gamemode.
    - For example, if you have a plugin ```permadeath.lua``` that defines a function called ```PLUGIN:PlayerDeath(client, inflictor, attacker)```, and another plugin called ```medical```, you can have a function in the ```medical``` plugin call the ```permadeath``` PlayerDeath hook without running any other code that has been hooked to PlayerDeath, via ```nut.plugin.list.permadeath:PlayerDeath(client, inflictor, attacker)```.

3. It allows to store variables specific to the plugin.
    - For example

    ```lua
    local curEnts = {}
    ```

    is a table only available within the file/scope where it was defined.

    However

    ```lua
    PLUGIN.curEnts = {}
    ```

    is a table that is available (within the respective realm) to all files/scopes within the plugin, as well as accessible from other plugins (```nut.plugin.list.permadeath.curEnts```).
## **Example**

An example of a plugin creation can be found [here](../development%20examples/Plugin%20Example.md).

_**It is recommended that you use plugins as puzzle pieces that fit into your gamemode. Creating plugins not only makes it easier to develop and enhance sections of your gamemode, your schema will remain organized as it gets larger.**_
