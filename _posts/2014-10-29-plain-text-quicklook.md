---
title: Preview any plain text file using OS X QuickLook
permalink: plaintext-quicklook
---

Use QuickLook to view any plain text, with or without an extension.

###QLStephen Plugin

* Download the QLStephen plugin: [Latest Release](https://github.com/whomwah/qlstephen/releases)

* Copy QLStephen.qlgenerator to `/Library/QuickLook/`

###Enable Selection in Finder 

`defaults write com.apple.finder QLEnableTextSelection -bool TRUE; qlmanage -r`

###Enable Selection in All Apps 

`defaults write -g QLEnableTextSelection -bool TRUE; qlmanage -r`

###QuickLook keyboard shortcut 

`⌘Y`

<br />
*Known to work on OS X Yosemite*

Sources: [QuickLook](https://coderwall.com/p/dlithw), [Selection](http://www.macworld.com/article/1164668/select_and_copy_text_within_quick_look_previews.html)

