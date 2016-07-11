---
title: STM32CubeMX Projects and GNU ARM Eclipse
excerpt: "Instructions to setup GNU ARM Eclipse with STM32CubeMX Projects on Mac OS X (macOS) or Linux"
permalink: cubemx-gnu-arm-eclipse
sitemap: true
---

## STM32CubeMX Project Export 

* Open STM32CubeMX and open the your project
	* in **Project -> Settings...**
	* select the **Project** tab
	* under **Toolchain / IDE**
	* select **SW4STM32**
	* hit **OK**
		* It will warn you that the previously generated files will be corrupted. That's ok. They're not there.
	* select **Project -> Generate Code**

## Import Project into Eclipse

Pretty simple:

* in the Project Exporer tab, right-click to show the context menu and select **Import...**
* under C/C++, select **Existing Code as Makefile Project**
* hit **Next**
* in the Import Existing Code dialog
	* name the project
	* hit **Browse** and select your repo top-level directory
	* under _Toolchain for Indexer Settings_, select **Cross ARM GCC**  
	* hit **Finish**

Configure the Project

* Enable Parallel Build
	* in **C/C++ Build**, select the **Behavior** tab
	* under _Build Settings_, check the **Enable parallel build** box, and select the **Use unlimited jobs** radiobutton
	* under _Workbench Build Behavior_, check the **Build on resource save (Auto build)** box
* Specify Build Configurations
	* in **C/C++ Build**, click the **Manage Configurations...** button
		* create a new configuration called `Release` by copying the current configuration
		* rename the current configuration to `Debug`
		* click **Ok**
	* with the **Debug** configuration selected,
		* select the **Behavior** tab
		* under _Workbench Build Behavior_, for the _Build_ field, enter `target=<_your_target_name_here_> build=Debug`
		* for the _Build on Resource Save (Auto Build)_, enter the same value from the _Build_ field
		* for the _Clean_ field, enter `clean target=<_your_target_name_here_>`
	* with the **Release** configuration selected,
		* select the **Behavior** tab
		* under _Workbench Build Behavior_, for the _Build_ field, enter `target=<_your_target_name_here_> build=Release`
		* for the _Build on Resource Save (Auto Build)_, enter the same value from the _Build_ field
		* for the _Clean_ field, enter `clean target=<_your_target_name_here_>`





