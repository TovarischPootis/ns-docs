# Factions

Factions are just a wrapper over the normal ```team``` library. Essentially, each faction associates a table with a team. So, extra information can be stored for teams (e.g. salary, descriptions, etc...).

## **Creating Factions**

To create a faction for your schema, first create a ```factions``` folder in your gamemode's ```schema``` folder if it does not exist. [Here](https://github.com/NutScript/skeleton-schema/tree/master/schema/factions) is an example.

One faction corresponds to one file within the ```factions``` folder. The files in the factions folder should be named ```sh_<identifier>.lua``` where ```<identifier>``` is a string containing only alphanumeric characters and underscores. By convention, ```<identifier>``` should be lowercase as well.

When the file is loaded, a global table called ```FACTION``` is available. This is the table that contains information about your faction. The following keys are required:

```lua
FACTION.name = "Faction Name"
FACTION.desc = "A description of your faction"
FACTION.color = Color(255, 255, 255)
```

Note that the ```name``` and ```desc``` are translatable.

Then, you can add any other details you would like for your faction.

At the end of the file, you must include the following:

```lua
TEAM_EXAMPLE = FACTION.index
```

The ```FACTION.index``` is a numeric value that is the team ID for your faction. This is the ID that is used with the [team](https://wiki.facepunch.com/gmod/team) library. So, here the ID is stored to a global variable for later use.

Now, your faction is done!

## **Player Models**

You can specify a list of available player models for your faction using ```FACTION.models```. This should be a table containing strings. For example:

```lua
FACTION.models = {
    "models/player/hostage/hostage_01.mdl",
    "models/player/hostage/hostage_02.mdl",
    "models/player/hostage/hostage_03.mdl",
    "models/player/hostage/hostage_04.mdl"
}
```

By default, ```FACTION.models``` is set to the list of HL2 citizen NPC models.

### **Skins**

An entry in ```FACTION.models``` can have a specific skin set by using a table containing two values instead of a string. The first value in the table should be the model path as a string. The second value should be a number containing the skin number. For example:

```lua
FACTION.models = {
    -- Prisoner guard Combine soldier (red eyes).
    {"models/player/combine_soldier.mdl", 1},
    -- Normal Combine soldier (blue eyes).
    "models/player/combine_soldier.mdl"
}
```

### **Bodygroups**

Similarly, an entry in ```FACTION.models``` can have certain bodygroups set. Instead of having a table with only two values, a table with 3 values is used. The third value is a string where the ith digit represents the value for the ith bodygroup. For example:

```lua
FACTION.models = {
    -- Metropolice with a manhack.
    {"models/police.mdl", 0, "01"},
    -- Normal metropolice without a manhack.
    "models/police.mdl"
}
```

## **Salary**

Characters within certain factions can be paid a salary. To set a salary for your faction, you just need to add the following line:

```lua
FACTION.pay = 123
```

The value for ```FACTION.pay``` is how much the character will be paid. By default, players are paid in intervals of the set value for the ```salaryInterval``` config (which by default is 300 seconds). However, you can change this yourself by adding the following line:

```lua
FACTION.payTime = 60
```

The value for ```FACTION.payTime``` is how often characters of this faction get paid in seconds.

If you want more control over salary payment, you can adjust the pay amount by returning a different amount in the ```GetSalaryAmount(client, faction)``` hook. You can also adjust the salary interval by returning a new interval in the ```GetSalaryInterval(client, faction)```hook.

## **Default Weapons**

You can specify a list of weapons to give to a player of a certain faction when they spawn by setting ```FACTION.weapons```. It should be set to table containing strings where the strings are the class of the weapons. For example:

```lua
FACTION.weapons = {"weapon_crowbar"}
```


## **How do I _ when a player in my faction spawns?**

Whenever a player of a specific faction spawns, the faction's onSpawn method is called with the player passed in. So, if you want something to happen when a player of your faction spawns, just add some behavior to the onSpawn method. For example:

```lua
FACTION.health = 150
FACTION.armor = 100

function FACTION:onSpawn(client)
    -- Custom health and armor for players in this faction.
    client:SetHealth(self.health)
    client:SetArmor(self.armor)
end
```

## Whitelists

If you only want factions to be accessible to players who are whitelisted for that faction, you should set

```lua
FACTION.isDefault = false
```

By default, ```FACTION.isDefault = true```. If ```FACTION.isDefault``` is set to true, a character could be created with that faction. If it is set to false, a character can only be created with that faction if the player creating the character is whitelisted for the faction.

## **Accessing Faction Data**

The ```FACTION``` table is no longer available after the file for the faction loads. However, you can access the faction data later in two ways:

* Using ```nut.faction.list``` or
* Using ```nut.faction.indices```

The "nice name" for a faction is the name of the faction file after the ```sh_``` prefix, but before the ```.lua``` extension. For example, if your faction file was called ```sh_citizen.lua```, the nice name is ```citizen```. You can access faction data using the nice name with ```nut.faction.list[niceName]``` where ```niceName``` is a string containing the nice name.

If you have the index of the faction (the value is ```FACTION.index``` that should have been stored in a global variable), you can use ```nut.faction.indices[index]``` where ```index``` is a number with the value of the faction index.

## **Player Limits**

You can limit the number of online players for a specific faction using ```FACTION.limit```. Simply set ```FACTION.limit``` to a number, where that number is the maximum number of players in that faction allowed at one time. For example:

```lua
FACTION.limit = 5
```

means only five players are allowed to be in that faction at one time.

In some cases, it may be more useful to have the limit as a percentage of the number of players on the server. Instead of using a whole number, simply use a decimal number less than 1. For example, to make it so only 1/4th of the players on the server are allowed to be in a specific faction, you would have

```lua
FACTION.limit = 0.25
```

If you need more control over the limit, you can use the ```FACTION:onCheckLimitReached(character, client)``` function. This is a function that should return ```true``` if the limit has been reached (that is, the player is not allowed to load a character of that faction), or ```false``` otherwise. For example, you could make the limit a percentage of the number of players on another faction:

```lua
function FACTION:onCheckLimitReached(character, client)
    return team.NumPlayers(self.index) < math.Round(team.NumPlayers(FACTION_OTHER) * 0.25)
end
```

Similarly, there is the ```CheckFactionLimitReached(faction, character, client)``` hook. It is similar to the function above, except ```faction``` is the faction table for some faction. The [default implementation](https://github.com/NutScript/NutScript/blob/4cc60462de847b253b7287dff8ab9c3d9cb1840b/gamemode/core/hooks/sh_hooks.lua#L304) actually implements the above features.