# gpSP makefile
# Gilead Kutnick - Exophase
# pandora port - notaz
# respberry pi - DPR

# Global definitions

CC        = /opt/miyoo/bin/arm-linux-gcc

OBJS      = main.o cpu.o memory.o video.o input.o sound.o gui.o \
            cheats.o zip.o arm_stub.o  warm.o cpu_threaded.o video_blend.o sha1.o imageio.o

BIN       = gpsp

# Platform specific definitions 

VPATH      += .. ../arm
CFLAGS     += -DARM_ARCH -DMIYOO -Wall
CFLAGS  += -march=armv5te -mtune=arm926ej-s -Ofast `/opt/miyoo/arm-miyoo-linux-uclibcgnueabi/sysroot/usr/bin/sdl-config --cflags`
LIBS       = -nodefaultlibs -lc -lgcc -lSDL -lpng -lasound -lz -Wl,--as-needed -Wl,--gc-sections -flto -s

# Compilation:

all: $(BIN)

%.o: %.S
	$(CC) $(CFLAGS) -c -o $@ $<


cpu.o cpu_threaded.o: CFLAGS += -Wno-unused-variable -Wno-unused-label

$(BIN): $(OBJS)
	$(CC) $(OBJS) $(LIBS) -o $(BIN) $(LDFLAGS)

clean:
	rm -f *.o $(BIN)
