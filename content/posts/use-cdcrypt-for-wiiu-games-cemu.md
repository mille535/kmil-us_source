---
title: "Use cdcrypt to convert Wii U games for Cemu"
date: 2022-03-21T23:23:44-05:00
draft: false
# weight: 1
# aliases: ["/first"]
author: "Kirk"
showToc: false
TocOpen: false
hidemeta: false
comments: false
description: "Run your legally dumped Wii U games on any PC"
canonicalURL: "https://blog.kmil.us/posts/use-cdecrypt-for-wiiu-games-cemu/"
disableHLJS: true # to disable highlightjs
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
categories: [Gaming]
tags: [Wii U, cemu, emulation]
#cover:
#    image: "<image path/url>" # image path/url
#    alt: "<alt text>" # alt text
#    caption: "<text>" # display caption under cover
#    relative: false # when using page bundles set this to true
#    hidden: true # only hide on current single page
#editPost:
#    URL: "https://github.com/<path_to_repo>/content"
#    Text: "Suggest Changes" # edit text
#    appendFilePath: true # to append file path to Edit link
---
With the Steam Deck coming out I find myself wanting to replay some of the Wii U classics (BoTW anyone?). While I don’t condone the use of ROM dump websites, I do understand why one might use them. I will not be going over the ROM dumping process from the Wii U in this post but may cover it in the future.

Once a ROM has been dumped from the Wii U it is still encrypted and needs to be decrypted before the game can be played with an emulator.

Downloads:
 - [cdecrypt](https://github.com/VitaSmith/cdecrypt/releases)
 - [Cemu](https://cemu.info)

 1. Extract cdecrypt archive. 
 2. Create a file `keys.txt` in the same folder as cdecrypt.exe.
 3. Locate a common Wii U key online and paste it into the first line of `keys.txt`. Optionally, find a Wii U common dev key and paste it into the second line of `keys.txt`.
 4. Run `cdecrypt.exe <input folder> <output folder>`to decrypt the game. 
 5. Add the newly decrypted game to cemu.
 6. Optional: explore graphics packs in cemu. 