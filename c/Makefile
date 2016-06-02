PROJ_NAME=C

# GNU ARM Embedded Toolchain
CC=arm-none-eabi-gcc
CXX=arm-none-eabi-g++
LD=arm-none-eabi-ld
AR=arm-none-eabi-ar
AS=arm-none-eabi-as
OBJCOPY=arm-none-eabi-objcopy
OD=arm-none-eabi-objdump
NM=arm-none-eabi-nm
SIZE=arm-none-eabi-size

###
# Directory Structure
BINDIR=bin
LINKSCRIPTPATH=linkscript

###
# Find source files
ASOURCES=$(shell find -L . -name '*.s')
CSOURCES+=$(shell find -L . -name '*.c')

# Find header directories
INC=$(shell find -L . -name '*.h' -exec dirname {} \; | uniq)
INCLUDES=$(INC:%=-I%)

# Create object list
OBJECTS=$(ASOURCES:%.s=%.o)
OBJECTS+=$(CSOURCES:%.c=%.o)

DEPSOBJ=$(CSOURCES:%.c=%.d)

# Define output files ELF & IHEX
BINELF=$(PROJ_NAME).elf
BINHEX=$(PROJ_NAME).hex


# MCU FLAGS
MCFLAGS=-mcpu=cortex-m4 -mthumb -mlittle-endian \
-mfpu=fpv4-sp-d16 -mfloat-abi=hard -mthumb-interwork
# COMPILE FLAGS
DEFS=-DUSE_STDPERIPH_DRIVER -DSTM32F4XX
CFLAGS +=-c $(MCFLAGS) $(DEFS) $(INCLUDES)

# LINKER FLAGS
LDSCRIPT= $(LINKSCRIPTPATH)/stm32_flash.ld
LDFLAGS =-T $(LDSCRIPT) $(MCFLAGS) --specs=nosys.specs

###
# Build Rules
.PHONY: all release release-memopt debug clean

all: release-memopt

release: $(BINDIR)/$(BINHEX)

release-memopt: CFLAGS+=-Os 
release-memopt: LDFLAGS+=-Os
release-memopt: release

debug: CFLAGS+=-g
debug: LDFLAGS+=-g
debug: release

$(BINDIR)/$(BINHEX): $(BINDIR)/$(BINELF)
	$(OBJCOPY) -O ihex $< $@
	@echo "Objcopy from ELF to IHEX complete!\n"

$(BINDIR)/$(BINELF): $(OBJECTS)
	$(CXX) $(LDFLAGS) $(OBJECTS) -o $@
	@echo "Linking complete!\n"
	$(SIZE) $(BINDIR)/$(BINELF)

%.o: %.c
	$(CC) $(CFLAGS) $< -o $@
	$(CC) -MM $(CFLAGS) $< > $*.d
	@echo "Compiled "$<"!\n"

%.o: %.s
	$(CC) $(CFLAGS) $< -o $@
	@echo "Assambled "$<"!\n"

clean:
	rm -f $(OBJECTS) $(DEPSOBJ) $(BINDIR)/$(BINELF) $(BINDIR)/$(BINHEX)