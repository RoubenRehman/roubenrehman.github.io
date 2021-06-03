---
layout: post
title: A fan-made Level Manager for Baba Is You
cover: transparent.png
date:   2020-09-06 21:00:00
categories: posts
---

## Download Level Manager [here](https://github.com/RoubenRehman/BabaIsYou-Level-Manager)


## Introduction

[Baba Is You](http://www.hempuli.com/baba/) is an award-winning puzzle game developed by [Arvi Teikari](http://www.hempuli.com/). The basic idea is the same as in every puzzle game, you have a certain objective, say a flag, and have to solve puzzles to get there. The catch: The game's rules are itself objects within the levels and can be altered. A wall blocks your way? Destroy the `Wall Is Stop` rule and just move *through* the wall! You have to move a rock, but can't? Just write `Rock Is Push` and try again! You get the idea.

[Baba is You - Trailer on YouTube](https://youtu.be/Ca5BYKRVXHM)

\\
The game's campaing offers a lot of content and I found myself playing for hours on end more than once, the head-cramping getting worse with each level. It's great fun for sure, but what is even more fun is not only to play, but *design* levels. This is what I no doubt love the most about this game; spending days designing a good level, trying to figure out new mechanics and how to decieve the player in the meanest ways possible. Then, when I'm done, sharing the level with my friends and watch them while their brains are slowly melting away. This quickly grew into a somewhat regular thing with me and some friends now meeting ever-so-often, dumping all of our levels on one machine and have each others play them.

### Sadly, the official level editor is not released yet, so exporting and sharing levels is a total mess
\\
Baba Is You has a level editor, the thing is its not officially released yet. To be able to use it, you have to alter the game's code in a certain way to make the game show it's debug features. How to do this is neatly explained in [this](https://steamcommunity.com/sharedfiles/filedetails/?id=1686041344) post by Steam User [Axonium](https://steamcommunity.com/id/Axonium). Once you activated the level editor, you can start creating your own levels.

**A small tip on the side:** when creating a new level, first hit the save button. Sometimes the editor freezes and you have to restart the game, delete the level and create a new one. This happening to you after saving a level you worked on for three hours can easily ruin your day, so be sure to check first.

Baba Is You saves levels created with the editor in its `world/levels/` folder. Each level has a `.l`, `.ld`, `.png` and sometimes `.ld.tmp` file associated with it. All those files have the same filename following a `<number>level` naming convention. This makes sharing levels difficult because one, the filename is (seemingly) random and not the in-game level title, and two, when copying level files to a different machine, the naming convention has to be kept while also ensuring to use a new number to not overwrite an already existing level. So to share a level, you have to

**\*** first *find* the files associated with the level you want to share\\
**\*** then zip them all together or move them individually to a different machine\\
**\*** and finally rename them correctly.

To make this process less daunting, at least until the oficial method of sharing is released, I designed a simple Level Manager that can be found on my GitHub [here](https://github.com/RoubenRehman/BabaIsYou-Level-Manager)

## Level Manager for Baba Is You

The Level Manager consists of a Python script and two UI files for the graphical user interface for which it utilizes the [PyQt5](https://pypi.org/project/PyQt5/) library. On first startup, you will be asked to set the path to your `steamapps/` folder. Baba Is You should be installed under `steamapps/common/Baba Is You`. The path will be saved in a .txt file in your user directory for future use.

The program then scans your `data/Worlds/levels/` folder and fetches the levels' titels from the `.ld` files. The levels get displayed in the ListView on the right site of the program.

![title](/images/Screenshot_levelmanag.png)

### Exporting

When exporting a level for the first time, the program asks you to set an exort path to write its zip archives to. This path will also be saved inside the txt file in your user directory and loaded on future startups. All files associated with the selected level will be zipped and the archive saved at the defined location. You can now share this and either extract the files manually or use the Level Manager to import the level for you.

### Importing

When clicking the import button, a file dialog will open. Just select the zip archive with the level files you want to import. The Level Manager will extract the files into the `/data/Worlds/levels/` folder, so they can be opened inside the ingame editor. But before doing so, the program checks if the filename of the to-be-imported level is already taken. If that is the case it automatically generates a `<number>level` filename, that isn't taken yet and renames the files before extracting them. This makes sure, no locally existing levels are overwritten by an imported one.

## Conclusion

I hope this makes life easier for some of you. If you find any bugs, it would be amazing if you could create an issue for them in the [GitHub Repo](https://github.com/RoubenRehman/BabaIsYou-Level-Manager).
