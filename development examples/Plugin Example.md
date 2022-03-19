# Plugin Example

We will take Permakill as an example of a simple plugin. This plugin automatically bans a character if it dies in certain ways.

## **Starting**

1. We will create a new file called ```permakill.lua``` in the plugins folder.
2. In it, we will add the following initial information:

    ```lua
    PLUGIN.name = "Permakill"
    PLUGIN.author = "Thadah Denyse"
    PLUGIN.desc = "Adds permanent death in the server options."
    ```

## **Configuration**

We want our plugin to be easily configurated in-game easily, like if it's active at all or dying by falling off a high place will effectively kill our character permanently. Fortunately, NutScript's Framework Libraries are very rich, and will allow us in this process.

In this case, we are going to use nut.config.add to add our in-game configuration for the server admin.

We want to be able to activate or deactivate permakill as we please, so we will make a configuration that allows us to do so:

```lua
nut.config.add("pkActive", false, "Whether or not permakill is activated on the server.", nil, {
    category = "Permakill"
})
```

* The first argument will be the key, which must the unique, therefore adding it a prefix (in this case pk) would be a good idea.

* The second argument is the value. In this case we will use a boolean value, and we will leave Permakill deactivated by default, therefore false

* The third argument is a description, so the admin knows what he is doing when changing the argument.

* The fourth argument is about our configuration using a callback. This doesn't apply to us so we will set it as nil

* The fifth argument is the data of the configuration, which is a table. In this case, we will set what category should the plugin have inside the Configuration tab, therefore {category = "Permakill"}

The same goes for the configuration about the world being able to permakill us:

```lua
nut.config.add("pkWorld", false, "Whether or not world and self damage produce permanent death.", nil, {
    category = "Permakill"
})
```

## **Functions**

To apply our configurations, we will want somewhere to call them. In this case we will call them inside functions, but it's not limited to it. You can call them from item functions, entities, derma and so on.

We will use the default Gmod functions [PlayerDeath](https://wiki.facepunch.com/gmod/GM:PlayerDeath) and [PlayerSpawn](https://wiki.facepunch.com/gmod/GM:PlayerSpawn), but prefixed with PLUGIN instead of GM, to prevent overriding them.

### **PlayerDeath**

First, the PlayerDeath function. The function will have the same arguments as its GM counterpart, therefore:

```lua
function PLUGIN:PlayerDeath(client, inflictor, attacker)
```

We don't want to affect our client, but the client's character, therefore we will create a local variable calling the Framework Class client:getChar:

```lua
function PLUGIN:PlayerDeath(client, inflictor, attacker)
    local character = client:getChar()
```

Next, we will want to know if Permakill is active in the server. This is where our Framework Library nut.config.get enters the scene:

```lua
function PLUGIN:PlayerDeath(client, inflictor, attacker)
    local character = client:getChar()

    if (nut.config.get("pkActive")) then
```

After that, we want to know if World Damage has been activated inside the server and we want to make it **not permanently kill people if it's deactivated**, therefore:

```lua
function PLUGIN:PlayerDeath(client, inflictor, attacker)
    local character = client:getChar()

    if (nut.config.get("pkActive")) then
        if not (nut.config.get("pkWorld") and (client == attacker or inflictor:IsWorld() then
            return
        end
```

The _return_ will invalidate everything that comes after that line, making the permakill ineffective if the World Damage is set to false

Now that we have verified everything we wanted, we will use character:setData so that we know that character has been permakilled, this ends the PlayerDeath function as well:

```lua
function PLUGIN:PlayerDeath(client, inflictor, attacker)
    local character = client:getChar()

    if (nut.config.get("pkActive")) then
        if not (nut.config.get("pkWorld") and (client == attacker or inflictor:IsWorld() then
            return
        end
        character:setData("permakilled", true)
    end
end
```

### **PlayerSpawn**

Now that we have set the ways the player is permakilled, we will set what happens when a permakilled player tries to spawn again.

First of all, we will get the player's character:

```lua
function PLUGIN:PlayerSpawn(client)
    local character = client:getChar()
```

After that, we will verify if permakill is active on the server and we will use character:getData to verify if the player has been permakilled. If both turn out to be true, we will ban that character with character:ban, so the player can't use it again:

```lua
function PLUGIN:PlayerSpawn(client)
    local character = client:getChar()
    if (nut.config.get("pkActive") and character and character:getData("permakilled")) then
        character:ban()
    end
end
```

This finishes our plugin. You can access the full code [here](https://github.com/NutScript/NutScript/blob/1.2/plugins/permakill.lua).