#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Author: Jesutofunmi Kupoluyi
# Date: 7 March, 2023
# 
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#        HOST 
#        MSP432
#
# Platform Overrides:
#        HOST
#        MSP432
#------------------------------------------------------------------------------

include sources.mk

#Variables
TARGET = c1m2

# Platform Overrides
PLATFORM =  

# Architectures Specific Flags
LINKER_FILE = ../msp432p401r.lds 
CPU = -mcpu=cortex-m4
ARCH = -march=armv7e-m
SPECS = --specs=nosys.specs

ifeq ($(PLATFORM),HOST)
    # Compiler Flags and Defines
    CC = gcc
    LD =
    LDFLAGS = -Wl,-Map=$(TARGET).map
    CFLAGS = -Wall -Werror -g -O0 -std=c99
    CPPFLAGs = -E 

else
    # Compiler Flags and Defines
    CC = arm-none-eabi-gcc
    LD = arm-none-eabi-ld
    LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
    CFLAGS = $(CPU) $(ARCH) $(SPEC) -mthumb -mfloat-abi=hard -mfpu=fpv4-sp-d16 -Wall -Werror -g -O0 -std=c99
    CPPFLAGs = -E

endif

#Getting all object files
OBJECTS = $(SOURCES:.c=.o)
PREPROCESSED = $(SOURCES:.c=.i)
ASSEMBLED = $(SOURCES:.c=.asm)
DEPENDENCIES = $(SOURCES:.c=.dep)

#Getting preprocessed files
%.i : %.c
	$(CC) -E $< $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o $@

#Getting assembly files
%.asm : %.c
	$(CC) -S $< $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o $@

#Getting the object files
%.o : %.c
	$(CC) -c $< $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o $@

#Compile all but do not link
.PHONY: compile-all
compile-all:
ifeq ($(PLATFORM), HOST)
	$(CC) -c main.c $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o main.o
	$(CC) -c memory.c $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o memory.o
else
	$(CC) -c main.c $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o main.o
	$(CC) -c memory.c $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o memory.o
	$(CC) -c interrupts_msp432p401r_gcc.c $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o interrupts_msp432p401r_gcc.o
	$(CC) -c startup_msp432p401r_gcc.c $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o startup_msp432p401r_gcc.o
	$(CC) -c system_msp432p401r.c $(CFLAGS) $(INCLUDES) -D$(PLATFORM) -o system_msp432p401r.o
endif
		

#Compile and link into a final executable
.PHONY: build
build: compile-all
	$(CC) $(OBJECTS)  $(CFLAGS) $(INCLUDES) $(LDFLAGS) -D$(PLATFORM)  -o $(TARGET).out 


#Clean (delete) all genereated files
.PHONY: clean
clean:
	rm -f $(OBJECTS) $(TARGET).out $(TARGET).map $(PREPROCESSED) $(ASSEMBLED) $(DEPENDENCIES)

