---
title: Minimal GNU ARM Eclipse Setup
excerpt: "Instructions to setup a minimal GNU ARM Eclipse installation on Mac OS X (macOS) or Linux"
permalink: gnu-arm-eclipse-setup-mars
sitemap: true
---

## Installation
This is how to install the most GNU ARM Eclipse development environment without any of the extra tools included in the standard Eclipse CDT distribution.

### Toolchain
Follow the instructions under the **Downloading** and **OS X** headings of the [Toolchain Installation Instructions](http://gnuarmeclipse.github.io/toolchain/install/).

### SEGGER J-Link
Download the **Software and Documentation Pack** for OS X from the [J-Link Software Site](https://www.segger.com/jlink-software.html).

### OpenOCD
From the GNU ARM Eclipse Installation Guide:

> OpenOCD might be needed when using development boards with integrated debuggers, like STM32F4-DISCOVERY boards, although the recommended solution is to prepare a custom cable and connect them to J-Link.

Download and install the .pkg from the [OpenOCD GitHub Releases](https://github.com/gnuarmeclipse/openocd/releases/latest) page. (Note that this may be a development release.)

### QEMU
Download and install the most recent .pkg release from the [QEMU GitHub Releases page](https://github.com/gnuarmeclipse/qemu/releases/latest).

### Eclipse
**DO NOT** get the Eclipse IDE for C/C++ Developers. While this will work fine, it includes a bunch of plugins that cannot be uninstalled later.

#### Eclipse Platform Runtime Binary
The Eclipse Platform Runtime Binary is the most stripped-down version of Eclipse available. Here's how to get it:
* Main downloads page: [http://download.eclipse.org/eclipse/downloads/](http://download.eclipse.org/eclipse/downloads/)
* Go to the most recent build (Latest Downloads → Build Name). The build number is the link, not the "(X of Y Platforms)" part -- that takes you to the test results report.
* Scroll down to "Platform Runtime Binary" and download the appropriate archive. Notice the size -- for Mars, it's 66M vs 203M for the entire SDK.

#### Install GNU ARM Eclipse Plug-ins

Since the Platform Runtime Binary doesn't include Eclipse Marketplace, use the Eclipse Update Site method as detailed under the heading "The Eclipse update site way" of the official [GNU ARM Eclipse Installation Instructions](http://gnuarmeclipse.github.io/plugins/install/), reprinted here:

##### Add Repository

<ul>
  <li>in the <em>Install</em> window, click the <strong>Add…</strong> button (on future updates, just select the URL in the <strong>Work with:</strong> combo)</li>
  <li>fill in <em>Name:</em> with <strong>GNU ARM Eclipse Plug-ins</strong></li>
  <li>fill in <em>Location:</em> with <strong>http://gnuarmeclipse.sourceforge.net/updates</strong></li>
  <li>click the <strong>OK</strong> button</li>
</ul>

##### Select Installation Items

* Cross Compiler
* Generic Cortex-M Template
* J-Link Debugging
* OpenOCD Debugging
* Packs
* QEMU Debugging
* STM32Fx Templates

#### Workspace Configuration

Follow the instructions here <http://gnuarmeclipse.github.io/eclipse/workspace/preferences/> with the following additions:

* Refresh using native hooks
* Refresh on access
* Disable spell checking
	* in  **General → Editors → Text Editors → Spelling**
	* uncheck **Enable spell checking**
* Disable Quick Diff
	* in  **General → Editors → Text Editors → Quick Diff**
	* uncheck **Enable quick diff**
* Disable indexing of files not included in the build
	* in **Preferences → C/C++ → Indexer**
	* uncheck **Index source files not included in the build**
* Mark occurences
	* in **Preferences → C/C++ → Editor**
	* uncheck **Keep marks when the selection changes**
* Don't load UI Responsiveness Monitoring Plug-in on startup
	* in **General → Startup and Shutdown**
	* uncheck **UI Responsiveness Monitoring**
* Menlo font (Mac OS X only)
	* in **General → Appearance → Colors and Fonts**
	* expand **Basic**
	* select **Text Font**
	* hit *Edit...* and select **Menlo**
* Make scrollbars visible always (Mac OS X only)
	* in your shell startup script, add: `defaults write -app Eclipse AppleShowScrollBars Always`
	* restart Eclipse
	* this improves scrolling performance related to [a 5+ year old bug in Eclipse](https://bugs.eclipse.org/bugs/show_bug.cgi?id=366471)




