# Developing the Schema

## **Initial Setup**

Make sure that you have the [Nutscript framework](https://github.com/NutScript/NutScript) already installed in the gamemodes folder (rename it to ```nutscript```).
In the same gamemodes plugin, place/create your schema folder, and name it as the name of your schema (hl2rp/cityrp/scprp)
![Example](https://i.imgur.com/bKgG8L0.png)

## **The Main Schema Folder**

Within the Schema folder, you must have 3 additional folders:

* gamemode
* plugins
* schema

    and one .txt file named identically to your schema name (that is, if your schema is called hl2rp, the txt must also be called hl2rp.txt)

_**As a Rule of Thumb, you should not modify the gamemode folder at any point. Please look at [pre-existing schemas](installation/Getting_Started.md#Schemas) and copy the gamemode folder from there.**_

* The ```plugins``` folder is used to install additional plugins to the schema.

* The ```schema``` folder contains the core files of your schema.

## **The Schema Folder**

Within the ```schema``` folder, you must have 2 crucial folders: ```items``` and ```factions```, as well as a crucial file called ```sh_schema.lua```

## **The Factions Folder**

The ```factions``` folder is used to create new factions playable on your gamemode. Each faction is represented by their individual file.

For example, if you want to have 3 factions called "Citizen", "Police" and "Insurgents", you would have 3 files within the factions folder

```
sh_citizen.lua
sh_police.lua
sh_insurgents.lua
```

Each file must contain the following code:

```lua
FACTION.name = "Custom Faction" -- the name of your faction as it appears in-game
FACTION.desc = "Custom faction for only some people to use." -- the faction's description, as seen in the character creation screen
FACTION.color = Color(255, 0, 0) -- the faction's color, as it appears in the scoreboard
FACTION.isDefault = false -- If this is set to true, anyone can create a character within this faction. If false, players would require a whitelist, or a character would require a transfer
FACTION.pay = 40 -- The default pay received every period of time.
FACTION.isGloballyRecognized = true -- Used by the recognition plugin. If this is set to true, characters within the faction are automatically recognized.


FACTION.weapons = { -- SWEPs that are given to members of the faction. Be advised that these are not items, rather SWEPs, therefore they do not fill the inventory, and cannot be dropped.
	"nut_stunstick"
}

FACTION.models = { -- default models available to the faction. The larger the list here, the bigger the options of models are for players when creating a new character
	"models/police.mdl",
	"models/alyx.mdl"
}

FACTION_CUSTOM = FACTION.index -- mandatory, this allows the faction to be indexed in code elsewhere. Replace FACTION_CUSTOM with FACTION_#### where #### is a unique string for your faction.
```

[https://github.com/rebel1324/NutScript/wiki/Factions More information and options available for factions is available here]

## **The Items Folder**

The ```items``` folder contains all the items that your schema provides.

There are 2 methods to adding items to your schema: single file items or via a base

### **Single File Items**

You can add an item as a single file by creating one in the ```items``` folder directly. For example, if you want to create a potato item, you would create a new file called ```sh_potato.lua```

Inside the file, you must have this code

```lua
ITEM.name = "A Potato" -- item name
ITEM.desc = "Boil em, Mash em, Stick em in a stew." -- item description
ITEM.price = 0 -- the default price, used by vendors and business tabs, for instance
ITEM.model = "models/props_junk/Shoe001a.mdl" -- the item's model
ITEM.category = "Other" -- the item's category
ITEM.uniqueID = "potato" -- optional field, used for ease of reference in code
-- you may add additional custom information that can be used by functions that use the item
ITEM.health = 10
```

If you want your items to have functionality, you may add functions via ```ITEM.functions.<name>```. For example

```lua
ITEM.functions.use = {
    name = "Consume", --Name of the function, if this doesn't exist it'll use "use"
    tip = "Consume the item.", --Tip when hovering over the function
    icon = "icon16/cup.png", --Icon for the function
    onRun = function(item, data)
        item.player:EmitSound("items/battery_pickup.wav") --Play a cool sound
        item.player:SetHealth(item.player:Health() + item.health) --Give health to player when they consume
        item.player:notifyLocalized("You consume "..string.lower(item.name).." for "..item.health.." health.") --Send message to player
                return true -- This is optional. Returning true in the function deletes the item from the inventory. This is useful if you want single-use items, however, if you want to avoid that, remove the return
    end
}
```

### **Using a Base**

Using a base is useful if you want multiple items that function relatively identical, such as foods or weapons.

To create items via a base, you need to create 2 folders: ```base``` and a folder with the base name. For example, if I want to create a food item base, I would have 2 folders named

```
base
food
```

Inside the base folder, create a lua file called the same as the target base folder (for instance if I am creating a food item base, I would create a file called ```sh_food.lua```)

Inside this new file goes the default information of the item. Items defined within the target base folder will inherit all the values and functions from this base file.

Let's create a food base

```lua
ITEM.name = "Food Base"
ITEM.model = "models/Gibs/HGIBS.mdl"
ITEM.desc = "Tasty food, or tasy drink."
ITEM.width = 1 -- used by the gridinv plugin, this allows to modify the size of the item within the inventory
ITEM.height = 1 -- used by the gridinv plugin, this allows to modify the size of the item within the inventory
ITEM.category = "Food"
ITEM.health = 0
ITEM.action = "consume"

--[[-------------------------------------------------------------------------
Purpose: Create a function that these items will use. ITEM.health and ITEM.action
were parameters that I created.
---------------------------------------------------------------------------]]
ITEM.functions.use = {
    name = "Consume", --Name of the function, if this doesn't exist it'll use "use"
    tip = "Consume the item.", --Tip when hovering over the function
    icon = "icon16/cup.png", --Icon for the function
    onRun = function(item, data)
        item.player:EmitSound("items/battery_pickup.wav") --Play a cool sound
        item.player:SetHealth(item.player:Health() + item.health) --Give health to player when they consume
        item.player:notifyLocalized("You "..string.lower(item.action).." "..string.lower(item.name).." for "..item.health.." health.") --Send message to player
    end
}
```

Now, in the food folder, we can create as many different food items as we please, and each will inherit these default values.

In the food folder, I will create a milk item. Call it ```sh_milk.lua```

```lua
ITEM.name = "Milk"
ITEM.model = "models/Gibs/HGIBS.mdl"
ITEM.desc = "Full fat cow's milk."
ITEM.width = 1
ITEM.height = 1
ITEM.health = 5
ITEM.action = "drink"
```

The new milk item will inherit everything from the food base, such as the consume function, however, we editted some values, such as health and action, so when the consume function is used on the item, it will use these values instead.

If you want multiple bases, create individual files in the base folder, and a new folder for each base type.

## **The ```sh_schema.lua``` File**

The sh_schema.lua file is a crucial part of the schema. Upon creating it, enter the following code:

```lua
SCHEMA.name = "Cool Schema" -- the Name of your Schema, as it appears in-game
SCHEMA.author = "You" -- the Author of the schema
SCHEMA.desc = "A schema for the people to learn from." -- A brief description of what the gamemode is about

nut.currency.set("$", "dollar", "dollars") --Symbol, singular tense of currency, plural tense of currency
```

While you can contain all of your code within this one file, you may want to create additional files in the schema and spread the code among them, for convenience and security.
To do this, use the following:

```lua
nut.util.include("sh_commands.lua") -- this allows code within the sh_commands.lua file to be readable by the schema.
nut.util.includeDir("hooks") -- this allows all files within the hooks folder in the schema folder to be readable by the schema
```

**Keep in mind that if you try to use functions or variables defined in these external files before they are included in the schema, they will not be available.**

Keep in mind that if you want to add folders that are default to nutscript, such as ```classes, meta``` or ```libs```, it is not necessary to include them.
