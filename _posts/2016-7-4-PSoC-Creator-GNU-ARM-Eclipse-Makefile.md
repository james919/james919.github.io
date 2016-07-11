---
title: Makefile for PSoC Creator projects in GNU ARM Eclipse
excerpt: "This is a Makefile suitable for PSoC Creator projects that uses build rules and GCC's autodependency feature."
permalink: psoc-makefile
sitemap: true
---

This is a Makefile suitable for PSoC Creator projects that uses build rules and GCC's autodependency feature. By passing in command line arguments, this makefile will build a Release or Debug binary.

See the section at the bottom of the listing, titled `Defaults`, for other options.

**NOTE** This makefile is specialized for the CY8C4247LQI-BL483 variant. You should customize this for your board as necessary.

```makefile

#########################################################################################################################################
# PSoC 4 Makefile
# 	- This makefile builds a PSoC Creator project with Debug and Release versions.
# 	- It will also flash the PSoC using J-Link. Haven't yet added this for the discovery version that uses an on-board JTAG programmer.
# 	- It uses GCC's autodependency feature to maintain a list of dependencies for each source file in the build directory -- this means
# 		it will recompile only the files that need to be recompiled, automatically.
# 	- The majority of the makefile is a defined variable that is evaluated with a set of parameters that can either be set at the bottom
# 		of this file or passed in via the command line. Evaluation happens at the end of the file, with defaults defined just before.
#########################################################################################################################################
# Use Bash
SHELL = /bin/sh

# Functions
find_includes_in_dir = $(shell find $(1) -name "*.h" | sed 's|/[^/]*$$||' | sort -u)

# -----------------------------------------------------------------------------
# Configuration Constants -- These are static and should not be discovered 
# 	using the greedy 'find's in the Build Configuration Rule defined below.
# -----------------------------------------------------------------------------
CYDEVICE		        := CY8C4247LQI-BL483
CYDSN_DIR	            := PSoC_Creator_Project_Directory.cydsn
CYGENERATED_SOURCE      := $(CYDSN_DIR)/Generated_Source/PSoC4
LDSCRIPTS               := $(addprefix -T, $(CYGENERATED_SOURCE)/cm0gcc.ld)
ASM_SRC                 := $(CYGENERATED_SOURCE)/CyBootAsmGnu.s

# -----------------------------------------------------------------------------------------------
# J-Link Configuration
# -----------------------------------------------------------------------------------------------
JLINK_ROOT              ?= /Applications/SEGGER/JLink
JLINKEXE                := $(JLINK_ROOT)/JLinkExe
JLINKGDBSERVER          := $(JLINK_ROOT)/JLinkGDBServer
JLINKDEVICE             := CY8C4247xxx-BLxxx
JLINKGDBCMD             := $(JLINKGDBSERVER) -if SWD -device $(JLINKDEVICE) >/tmp/jlink.log 2>&1

# ---------------------------------------------------------------------
# Toolchain Configuration
# ---------------------------------------------------------------------
BINUTILS_ROOT           ?= /usr/local/gcc-arm-none-eabi-5_3-2016q1
TOOLCHAIN               := arm-none-eabi
CC                      := $(BINUTILS_ROOT)/bin/$(TOOLCHAIN)-gcc
CXX                     := $(BINUTILS_ROOT)/bin/$(TOOLCHAIN)-g++
AS                      := $(BINUTILS_ROOT)/bin/$(TOOLCHAIN)-as
OBJCOPY					:= $(BINUTILS_ROOT)/bin/$(TOOLCHAIN)-objcopy
SIZE 					:= $(BINUTILS_ROOT)/bin/$(TOOLCHAIN)-size
CYELFTOOL               := $(CYDSN_DIR)/Export/CyElfTool.exe
WINE                    := /usr/local/bin/wine
C_STANDARD				:= -std=gnu11
CXX_STANDARD 			:= -std=gnu++11

# -----------------------------------------------------------------------------------------------------------------
# Defined Symbols
# -----------------------------------------------------------------------------------------------------------------
DEFS 					:=
#-DSTM32F437xx -DUSE_HAL_DRIVER -DARM_MATH_CM4 -D__FPU_PRESENT=1U -DHSE_VALUE=25000000

# ---------------------------------------------------------------------------------------------------------------------------------------
# Compiler & Linker Flags
# ---------------------------------------------------------------------------------------------------------------------------------------
# Flags sent to all tools in the Toolchain (except Assembler)
TOOLCHAIN_SETTINGS 		:= -mcpu=cortex-m0 -march=armv6-m -mthumb
TOOLCHAIN_SETTINGS 		+= -fmessage-length=0 -fsigned-char -ffunction-sections -fdata-sections -ffreestanding -fno-move-loop-invariants

# Assembler -- Required Flags
ASFLAGS 				+= -mcpu=cortex-m0 -march=armv6-m -mthumb
ASFLAGS                 += $(addprefix -I, $(INC_DIRS))

# gcc flags
CFLAGS 					+= $(TOOLCHAIN_SETTINGS) $(DEFS) $(addprefix -I, $(INC_DIRS))
CFLAGS                  += -Wall
CFLAGS 					+= -Wextra
CFLAGS 					+= -Wfatal-errors
CFLAGS 					+= -Wpacked
CFLAGS 					+= -Winline
CFLAGS 					+= -Wfloat-equal
CFLAGS 					+= -Wlogical-op
CFLAGS 					+= -Wdisabled-optimization
CFLAGS                	+= -Wno-unused-parameter
CFLAGS                  += -Wa,-alh=$(@:.o=.lst)

# C++ Compiler -- Required & Optimization Flags
CXXFLAGS                += $(CFLAGS)
CXXFLAGS 				+= -fabi-version=0
CXXFLAGS                += -fno-rtti
CXXFLAGS                += -fno-exceptions
CXXFLAGS				+= -fno-use-cxa-atexit
CXXFLAGS 				+= -fno-threadsafe-statics

# C++ -- Warnings
CXXFLAGS 				+= -Weffc++
CXXFLAGS 				+= -Wfloat-equal
CXXFLAGS 				+= -Wsign-promo
CXXFLAGS 				+= -Wzero-as-null-pointer-constant
CXXFLAGS 				+= -Woverloaded-virtual
CXXFLAGS 				+= -Wsuggest-final-types
CXXFLAGS 				+= -Wsuggest-final-methods
CXXFLAGS 				+= -Wsuggest-override
CXXFLAGS 				+= -Wsuggest-attribute=pure
CXXFLAGS 				+= -Wsuggest-attribute=const
CXXFLAGS 				+= -Wsuggest-attribute=noreturn
CXXFLAGS 				+= -Wsuggest-attribute=format
CXXFLAGS 				+= -Wmissing-format-attribute
CXXFLAGS 				+= -Wold-style-cast
CXXFLAGS 				+= -Wshadow
CXXFLAGS 				+= -Wctor-dtor-privacy
CXXFLAGS 				+= -Wstrict-null-sentinel

# Linker
LDFLAGS 				+= $(TOOLCHAIN_SETTINGS) $(DEFS) -Xlinker --gc-sections --specs=nano.specs

# CyElfTool flags
CYFLAGS					+= --flash_row_size 128 --flash_size 131072

# -------------------------------------------------------------
# Build Type Modifiers
# -------------------------------------------------------------
# Debug
DEFS_DEBUG 				+= -DDEBUG -DTRACE -DOS_USE_TRACE_ITM
CFLAGS_DEBUG            += -ggdb -g3 -Og
LDFLAGS_DEBUG			+= --specs=rdimon.specs -Og 

# Release
CFLAGS_RELEASE			+= -Os
LDFLAGS_RELEASE 		+= --specs=nosys.specs

# -------------------------------------------------------------------------------
# J-Link Flash Command
# 	The following command string (between define/endef) flashes a code to a PSoC4
# 	The trick here is to define a multi-line variable in make and then export it
# 		to the subshell that will run JLinkExe
# -------------------------------------------------------------------------------
define JLINK_FLASH_PSoC4_CMD
speed 1000
if SWD
device $(JLINKDEVICE)
halt
r
erase
loadfile $(BUILD_DIR)/$(PRODUCT).hex
r
g
exit
endef
export JLINK_FLASH_PSoC4_CMD

#########################################################################################################################################
# RULE DEFINITIONS -- This section is generic
#########################################################################################################################################

# =======================================================================================================================================
# Build Configuration Rule 
# - Generate build config using Product Root Directory ($1), Build Type ("Debug" or "Release") ($2)
# =======================================================================================================================================
define CONFIG_RULE
BUILD_DIR 				:= $1/Build/$2
OBJ_DIR 				:= $$(BUILD_DIR)/obj
INC_DIRS 				:= $$(call find_includes_in_dir, $$(SRC_DIRS)) $(CYDSN_DIR)
HEADERS 				:= $$(foreach dir, $$(SRC_DIRS), $$(shell find $$(dir) -name "*.h"))
C_SRC					:= $$(foreach dir, $$(SRC_DIRS), $$(shell find $$(dir) -name "*.c" -not -name "*main.c"))
CXX_SRC					:= $$(foreach dir, $$(SRC_DIRS), $$(shell find $$(dir) -name "*.cpp"))
CYLIBS                  := $$(shell find $$(CYDSN_DIR)/Export -name "*.a")
OBJECTS                 := $$(addprefix $$(OBJ_DIR)/, $$(C_SRC:.c=.o) $$(CXX_SRC:.cpp=.o) $$(ASM_SRC:.s=.o))
AUTODEPS 				:= $$(OBJECTS:.o=.d)
DIRS 					:= $$(BUILD_DIR) $$(sort $$(dir $$(OBJECTS)))

ifeq ($2, Release)
	DEFS 	+= $$(DEFS_RELEASE)
	CFLAGS 	+= $$(CFLAGS_RELEASE)
	LDFLAGS += $$(LDFLAGS_RELEASE)
else 
	DEFS 	+= $$(DEFS_DEBUG)
	CFLAGS 	+= $$(CFLAGS_DEBUG)
	LDFLAGS += $$(LDFLAGS_DEBUG)
endif

endef 
# =======================================================================================================================================
# End CONFIG_RULE
# =======================================================================================================================================

# =======================================================================================================================================
# Build Target Rule 
# - Generate build config using Product Name ($1), Product Root Directory ($2), Build Type ("Debug" or "Release") ($3)
# =======================================================================================================================================
define BUILD_TARGET_RULE
$(eval $(call CONFIG_RULE,$2,$3))

all : $$(BUILD_DIR)/$1.hex

# Tool Invocations
%.hex : %.elf
	@echo 'Invoking: CyElfTool.exe'
	@WINEDEBUG=-all $$(WINE) $$(CYELFTOOL) -C $$(<) $$(CYFLAGS) 
	@WINEDEBUG=-all $$(WINE) $$(CYELFTOOL) -S $$(<)
	@echo 'Finished building: $$@'
	@echo ' '
	@echo 'Invoking: Cross ARM GNU Print Size'
	$$(SIZE) --format=berkeley $$<
	@echo 'Finished building: $$@'
	@echo ' '

$$(BUILD_DIR)/$1.elf : $$(OBJECTS) | $$(BUILD_DIR)
	@echo 'Building $$(@)'
	@echo 'Invoking: Cross ARM C++ Linker'
	@touch cycodeshareexport.ld cycodeshareimport.ld
	$$(CXX) \
		-Xlinker -Map=$$(patsubst %.elf,%.map,$$(@)) \
		$$(LDFLAGS) \
		$$(LDSCRIPTS) \
		-o $$(@) $$(OBJECTS) $$(CYLIBS)
	@rm cycodeshareexport.ld cycodeshareimport.ld
	@echo 'Finished building: $$@'
	@echo ' '

$$(OBJECTS) : | $$(DIRS)

$$(DIRS) : 
	@echo Creating $$(@)
	@mkdir -p $$(@)

$$(OBJ_DIR)/%.o : %.c
	@echo Compiling $$(<F)
	@$$(CC) $$(C_STANDARD) $$(CFLAGS) -c -MMD -MP $$< -o $$(@)

$$(OBJ_DIR)/%.o : %.cpp
	@echo Compiling $$(<F)
	@$$(CXX) $$(CXX_STANDARD) $$(CXXFLAGS) -c -MMD -MP $$< -o $$(@)

$$(OBJ_DIR)/%.o : %.s
	@echo Assembling $$(<F)
	@$$(AS) $$(ASFLAGS) $$< -o $$(@)

flashit : $$(BUILD_DIR)/$1.hex
	@echo "$$$$JLINK_FLASH_PSoC4_CMD" | $$(JLINKEXE)

$$(BUILD_DIR)/.gdbinit : $$(BUILD_DIR)
	@rm -f $$(@)
	@echo "shell $$(JLINKGDBCMD)&" 	>> $$(@)
	@echo "target remote :2331" 	>> $$(@)
	@echo "monitor reset" 			>> $$(@)

clean :
	@echo 'Cleaning build directory...'
	rm -rf $$(PRODUCT_DIR)/Build
	@echo 'Finished cleaning.'
	@echo ' '

.PHONY : clean all

# include by auto dependencies
-include $$(AUTODEPS)

endef
# =======================================================================================================================================
# End BUILD_TARGET_RULE
# =======================================================================================================================================
#########################################################################################################################################
#########################################################################################################################################

# Build Type
ifeq ($(build), Release)
	BUILD_TYPE := Release
else
	BUILD_TYPE := Debug
endif

# Defaults
PRODUCT ?= Product_Name
PRODUCT_DIR ?= .
BUILD_TYPE ?= Debug
SRC_DIRS ?= $(CYGENERATED_SOURCE) src


# Evaluate Rules Defined Above
$(eval $(call BUILD_TARGET_RULE,$(PRODUCT),$(PRODUCT_DIR),$(BUILD_TYPE)))


```


