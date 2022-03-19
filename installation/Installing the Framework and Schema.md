# Installing the Framework and Schema

Once you have both the framework and schema downloaded, locate the gamemodes folder in your garrysmod folder. This is located in the server's root folder (where the srcds executable is located).

![image](https://i.imgur.com/A3cLjbY.png)

## Installing the Framework

1. Place the downloaded framework folder into the gamemodes folder.
2. Rename the framework folder to nutscript. (Case is important, as most server hosts run on Linux, which is case-sensitive)

## Installing the Schema

1. Place the downloaded schema folder into the gamemodes folder.
2. Rename the schema folder into whatever you want your gamemode to be.
3. Open the schema folder and locate the .txt file. Rename it to the same name as your schema folder name.
4. Open the .txt file. It should look like this

    ```json
    "skeleton"
    {
        "base"      "nutscript"
        "title"     "Skeleton"
        "author"    "Zoephix"
        "menusystem"  "1"
        "category"  "rp"
    }
    ```

    * ```skeleton``` is replaced by the name of your schema
    * ```title``` is the presented title of the gamemode as seen by the gamemode selection bar on the bottom-right corner of the menu
    * ```author``` is the name of the schema's author
    * ```menusystem``` defines whether the gamemode/server should appear in the multiplayer server list.
    * ```category``` is the category the gamemode is in.

    Rename the first line to the name of your schema and optionally the author. Keep the rest as is.

![image](https://i.imgur.com/AeDDW7Z.png)

### Congratulations, you have successfully installed NutScript

If you wish to further develop and/or modify your schema and gamemode, carry on over to [Developing the Schema](installation/Developing_the_Schema.md).