---
title: Using GNU ARM Eclipse with PSoC Creator Projects
excerpt: "Instructions to setup GNU ARM Eclipse with PSoC Creator Projects on Mac OS X (macOS) or Linux"
permalink: gnu-arm-eclipse-psoc-creator
sitemap: true
---

This is how to use GNU ARM Eclipse instead of PSoC Creator to develop for Cypress PSoCs. 

Caveats:

* Requires the use of virtualized Windows and PSoC Creator to configure your project and generate source 
* There is one tool in the build process that must be invoked using Wine: `CyElfTool.exe` -- this tool does something similar to `objcopy`, but produces markedly different .elf and .bin files.

## Prerequisites

#### VirtualBox

* Download and install the **VirtualBox Platform Package** and the **VirtualBox Extension Pack** from the [VirtualBox Downloads Page](https://www.virtualbox.org/wiki/Downloads).
* Setup a shared directory between VirtualBox and the host, like `~/VirtualBoxShared` 

#### Windows 7

Microsoft has made virtualizing Windows freely available through the Microsoft Edge developer site.

* Download the **IE 11 on Win7** for **VirtualBox** archive from the [Microsoft Edge Developer Site](https://developer.microsoft.com/en-us/microsoft-edge/tools/vms/mac/).
* Disable Windows Update by following the instructions [here](http://www.techtalkz.com/windows-7/515869-windows-update-enable-disable-automatic-updates-windows-7-guide.html) and [here](https://www.youtube.com/watch?v=vWd4k2OggJY).
* Create a VirtualBox Snapshot when you have everything configured.

#### PSoC Creator

Cypress requires a developer account to access their tool and documentation.

* Download and install [PSoC Creator](http://www.cypress.com/products/psoc-creator-integrated-design-environment-ide) in Windows.

#### Homebrew

If you don't already have it, install Homebrew by pasting this into your terminal: 

`/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"`

#### Wine

* Install using Homebrew (takes a little while): `brew install wine`
* When finished, type `winecfg` at the command prompt. Wine will setup its default config in `~/.wine/`, and will then be usable.

<hr />

## PSoC Creator Project Export

* in PSoC Creator, right-click on your project and select **Export to IDE (*Project Name*)...**
* select **Eclipse** and hit **Next**
* on the next page, hit **Export**
* copy the (*ProjectName*.cydn) directory over to your VirtualBox shared directory

## GNU ARM Eclipse Project Setup

Once you have your environment setup and a PSoC Creator project exported, you're ready to import it into GNU ARM Eclipse.

#### Project Directory Structure

In the File System:

* Create a new root directory to hold the exported PSoC Creator project
* Copy the exported project (*ProjectName*.cydsn) into the root directory
* Copy the makefile into the root directory
* In the root directory, create a new user source directory named `source`. This is where any new code written in GNU ARM Eclipse goes.

#### Import Existing Code

In Eclipse:

* select **Makefile Project with Existing Code** 
* give your project a name, and select the project root directory as the **Existing Code Location**.
* select **Cross ARM GCC** for **Toolchain for Indexer Settings**
* click **Finish**

#### Configure Eclipse Project

###### Populate Build Environment Variables

For whatever reason, the build environment variables are not populated when you select the toolchain in the project import dialog.

In Project Preferences:

* in **Preferences → C/C++ Build → Settings**
* select the **Toolchains** tab
* for field *Name*, select **GNU Tools for ARM Embedded Processors (arm-none-eabi-gcc)**
	* this is because of a strange bug -- the build environment variables are not set until this selection is made.

###### Tell the Indexer Where Code is Located

At this point, the project will build correctly, but the indexer has no idea where all the code you're referencing is located. 

In Project Preferences:

* in **Preferences → C/C++ General → Paths and Symbols**
* select the **Includes** tab
* click **Add...**
* check all 3 boxes ("Add to all configurations", "Add to all languages", "Is a workspace path")
* click **Workspace...**
* select your project root directory
* hit **OK**

## Makefile 

For your specific project, look at the console output of the PSoC Creator build. 

Example makefile [here]({{ baseurl }}/psoc-makefile/)

