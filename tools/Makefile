#工程名称
TARGET 			= MRX_SOCKET

#设置编译器
#CC    			= arm-linux-gnueabihf-gcc
CC     			= mipsel-openwrt-linux-uclibc-gcc

#设置编译器连接库路径
LIBPATH			= /opt/openwrt/openwrt_CC_mt76xx_zhuotk_source/staging_dir/target-mipsel_24kec+dsp_uClibc-0.9.33.2/usr/

#获取当前工作目录
TOP=.

#设置源文件后缀，c或cpp
EXT				= c

#设置源文件搜索路径
#VPATH			+= $(TOP)/app:$(TOP)/hal:$(TOP)/osal

VPATH			+= $(TOP)/app:$(TOP)/tools
#设置自定义源文件目录
APP_DIR			= $(TOP)/app
#HARD_DIR		= $(TOP)/hal

#设置中间目标文件目录
OBJ_DIR			= $(TOP)/obj

#设置拷贝到OPENWRT的PACKET
TARPATH			= /opt/openwrt/openwrt_CC_mt76xx_zhuotk_source/package/mrx_pos_packet/src/build/

#设定头文件包含目录
INC_FLAGS 		+= -I $(TOP)/app
INC_FLAGS		+= -I $(TOP)/tools
#INC_FLAGS 		+= -I $(TOP)/osal
#INC_FLAGS 		+= -I $(TOP)/hal
INC_FLAGS		+= -I $(LIBPATH)include
#编译选项
CFLAGS 			+= -W -g -O0 -std=gnu11

#链接选项
LFLAGS 			+= -pthread 
LFLAGS			+= -lmosquitto
LFLAGS			+= -L$(LIBPATH)lib
LFLAGS			+= -Wl,-rpath=$(LIBPATH)lib
#LFLAGS			+= -L$(LIBPATH)lib -Wl,-rpath,$(LIBPATH)lib
#固定源文件添加
C_SRC			+= $(shell find $(TOP)/app -name '*.$(EXT)')
C_SRC			+= $(shell find $(TOP)/tools -name '*.$(EXT)')
#C_SRC			+= $(shell find $(TOP)/hal -name '*.$(EXT)')
#C_SRC			+= $(shell find $(TOP)/osal -name '*.$(EXT)')

#自定义源文件添加
# C_SRC			+= $(HARD_DIR)/uart.c $(APP_DIR)/ipc_udp.c

#中间目标文件
#C_OBJ			+=$(C_SRC:%.$(EXT)=%.o)
C_SRC_NODIR		= $(notdir $(C_SRC))
C_OBJ 			= $(patsubst %.$(EXT), $(OBJ_DIR)/%.o,$(C_SRC_NODIR))

#依赖文件
C_DEP			= $(patsubst %.$(EXT), $(OBJ_DIR)/%.d,$(C_SRC_NODIR))

.PHONY: all clean rebuild ctags

all:$(C_OBJ)
	@echo "linking object to $(TARGET).bin"
	@$(CC) $(C_OBJ) -o $(TARGET).bin $(LFLAGS)
	@cp $(TARGET).bin $(TARPATH)$(TARGET)

$(OBJ_DIR)/%.o:%.$(EXT)
	@mkdir -p obj
	@echo "building $<"
	@$(CC) -c $(CFLAGS) $(INC_FLAGS) -o $@ $<

-include $(C_DEP)
$(OBJ_DIR)/%.d:%.$(EXT)
	@mkdir -p obj
	@echo "making $@"
	@set -e;rm -f $@;$(CC) -MM $(CFLAGS) $(INC_FLAGS) $< > $@.$$$$;sed 's,\($*\)\.o[ :]*,$(OBJ_DIR)/\1.o $(OBJ_DIR)/\1.d:,g' < $@.$$$$ > $@;rm -f $@.$$$$

clean:
	-rm -f obj/*
	-rm -f $(shell find ./ -name '*.bin')

rebuild: clean all

ctags:
	@ctags -R `pwd`
	@echo "making tags file"
