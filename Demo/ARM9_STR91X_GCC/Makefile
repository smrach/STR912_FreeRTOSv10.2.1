# File: Makefile
#
# Project: STR91x Eclipse demo
#
# Description: this makefile build the demo with the lwIP tasks.
#
# Created: 27/08/2008
#
# Author: Stefano Oliveri
# E-mail: software@stf12.net
# Modified by :Stefano Mrach 2019

# Project name
PROJECT_NAME=RTOSDemo

GCC_BASE = D:/Compilers/em_armgcc/
GCC_BIN = $(GCC_BASE)bin/
GCC_LIB = $(GCC_BASE)arm-none-eabi/lib/
GCC_INC = $(GCC_BASE)arm-none-eabi/include/

BASE_PATH=D:/GITHUB/STR912_FreeRTOSv10.2.1
DEMO_PATH=$(BASE_PATH)/Demo/ARM9_STR91X_GCC

# Project specific PATH
RTOS_SOURCE_DIR=$(BASE_PATH)/Source
#ST_LIB_DIR=$(DEMO_PATH)/Library
ST_LIB_DIR=$(DEMO_PATH)/STR91xFWLib/v2.0
#DEMO_COMMON_DIR=$(DEMO_PATH)/Common/Minimal
#DEMO_INCLUDE_DIR=$(DEMO_PATH)/Common/include

#USE_THUMB_MODE = YES
USE_THUMB_MODE = NO

AS = $(GCC_BIN)arm-none-eabi-as
CC = $(GCC_BIN)arm-none-eabi-gcc
CPP = $(GCC_BIN)arm-none-eabi-g++
LD = $(GCC_BIN)arm-none-eabi-gcc
OBJCOPY = $(GCC_BIN)arm-none-eabi-objcopy
OBJDUMP = $(GCC_BIN)arm-none-eabi-size
NM = $(GCC_BIN)arm-none-eabi-nm
LDSCRIPT=STR91x-ROM.ld

ifeq ($(USE_THUMB_MODE),YES)
THUMB    = -mthumb
THUMB_IW = -mthumb-interwork
THUMB_D = THUMB_INTERWORK
THUMB_CLEAN = 
else 
THUMB    = 
THUMB_IW = 
THUMB_D = NO_THUMB_INTERWORK
THUMB_CLEAN = 
endif

# should use --gc-sections but the debugger does not seem to be able to cope with the option.
LINKER_FLAGS=  $(THUMB) -nostartfiles -Xlinker -o$(PROJECT_NAME).axf -Xlinker -M -Xlinker -Map=$(PROJECT_NAME).map -Xlinker --no-gc-sections

DEBUG=-g
OPTIM=-O0

CFLAGS= $(DEBUG) \
		$(OPTIM) \
		-T$(LDSCRIPT) \
		-I . \
		-I LcdCode \
		-I $(ST_LIB_DIR)/inc \
		-I $(RTOS_SOURCE_DIR)/include \
		-I $(RTOS_SOURCE_DIR)/portable/GCC/STR9x \
		-D STR91X_GCC \
		-D $(THUMB_D) \
		-mcpu=arm966e-s \
		-D PACK_STRUCT_END=__attribute\(\(packed\)\) \
		-D ALIGN_STRUCT_END=__attribute\(\(aligned\(4\)\)\) \
		-fomit-frame-pointer \
		-ffunction-sections \
		$(THUMB_IW)

										
THUMB_SOURCE= \

ARM_SOURCE= \
		91x_it.c  \
		stf_stdlib.c \
		printf.c \
		$(RTOS_SOURCE_DIR)/portable/GCC/STR9x/port.c \
		$(RTOS_SOURCE_DIR)/portable/GCC/STR9x/portISR.c \
		main.c \
		LcdCode\lcd.c \
		$(ST_LIB_DIR)/src/91x_lib.c \
		$(ST_LIB_DIR)/src/91x_scu.c \
		$(ST_LIB_DIR)/src/91x_gpio.c \
		$(ST_LIB_DIR)/src/91x_fmi.c \
		$(ST_LIB_DIR)/src/91x_tim.c \
		$(ST_LIB_DIR)/src/91x_uart.c \
		$(ST_LIB_DIR)/src/91x_wdg.c \
		$(ST_LIB_DIR)/src/91x_vic.c \
		$(ST_LIB_DIR)/src/91x_ssp.c \
		$(RTOS_SOURCE_DIR)/list.c \
		$(RTOS_SOURCE_DIR)/queue.c \
		$(RTOS_SOURCE_DIR)/tasks.c \
		$(RTOS_SOURCE_DIR)/portable/MemMang/heap_2.c \
		
THUMB_OBJS = $(THUMB_SOURCE:.c=.o)
ARM_OBJS = $(ARM_SOURCE:.c=.o)

LIBS= 

all: $(PROJECT_NAME).bin

$(PROJECT_NAME).bin : $(PROJECT_NAME).axf
	$(OBJCOPY) $(PROJECT_NAME).axf -O binary $(PROJECT_NAME).bin
	 

$(PROJECT_NAME).axf : $(THUMB_OBJS) $(ARM_OBJS) startup.s vector.s Makefile
	$(CC) $(CFLAGS) $(ARM_OBJS) $(THUMB_OBJS) $(LIBS) startup.s vector.s $(LINKER_FLAGS) 


$(THUMB_OBJS) : %.o : %.c Makefile FreeRTOSConfig.h
	$(CC) -c $(CFLAGS) -mthumb $< -o $@

$(ARM_OBJS) : %.o : %.c Makefile FreeRTOSConfig.h
	$(CC) -c $(CFLAGS) $< -o $@

	
clean :
	$(THUMB_CLEAN)
	del *.o -r
 
log : $(PROJECT_NAME).axf
	$(NM) -n $(PROJECT_NAME).axf > $(PROJECT_NAME)SymbolTable.txt
	$(OBJDUMP) $(PROJECT_NAME).axf > $(PROJECT_NAME)MemoryListing.txt

