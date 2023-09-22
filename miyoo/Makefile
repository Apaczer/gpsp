# gpSP makefile
# Gilead Kutnick - Exophase
# pandora port - notaz
# respberry pi - DPR
# bittboy - steward'fu & gameblabla
# miyoo - Apaczer

# Global definitions
HOMEPATH = /mnt
TARGET ?= gpsp
VERSION ?= $(shell date +%Y-%m-%d\ %H:%M)
RELEASEDIR = package
ASSETSDIR = assets
OPKG_ASSETSDIR = opkg_assets
LINK = gpsp.lnk
DESTDIR = emus
SECTION = emulators
ALIASES = aliases.txt

CC        = /opt/miyoo/bin/arm-linux-gcc

OBJS      = main.o cpu.o memory.o video.o input.o sound.o gui.o \
            cheats.o zip.o arm_stub.o  warm.o cpu_threaded.o video_blend.o sha1.o imageio.o

BIN       = $(TARGET)

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

config:
	@cat ../game_config.txt > $(ASSETSDIR)/game_config.txt

package: all config
	@mkdir -p $(RELEASEDIR)
	@cp *$(TARGET) $(RELEASEDIR)/
	@mkdir -p $(RELEASEDIR)/$(DESTDIR)/$(TARGET)
	@mkdir -p $(RELEASEDIR)/gmenu2x/sections/$(SECTION)
	@mv $(RELEASEDIR)/*$(TARGET) $(RELEASEDIR)/$(DESTDIR)/$(TARGET)/
	@cp -r $(ASSETSDIR)/* $(RELEASEDIR)/$(DESTDIR)/$(TARGET)
	@cp $(OPKG_ASSETSDIR)/$(LINK) $(RELEASEDIR)/gmenu2x/sections/$(SECTION)
	@sed "s/^exec=.*/exec=\$(HOMEPATH)\/$(DESTDIR)\/$(TARGET)\/$(TARGET)/" $(OPKG_ASSETSDIR)/$(LINK) > $(RELEASEDIR)/gmenu2x/sections/$(SECTION)/$(LINK)
	@cp $(OPKG_ASSETSDIR)/$(ALIASES) $(RELEASEDIR)/$(DESTDIR)/$(TARGET)

zip: package
	@cd $(RELEASEDIR) && zip -rq $(TARGET)$(VERSION).zip ./* && mv *.zip ..
	@rm -rf $(RELEASEDIR)

ipk: package
	@mkdir -p .$(HOMEPATH)
	@mv $(RELEASEDIR)/* .$(HOMEPATH) && mv .$(HOMEPATH) $(RELEASEDIR)
	@mkdir -p $(RELEASEDIR)/data
	@mv $(RELEASEDIR)$(HOMEPATH) $(RELEASEDIR)/data/
	@cp -r $(OPKG_ASSETSDIR)/CONTROL $(RELEASEDIR)
	@sed "s/^Version:.*/Version: $(VERSION)/" $(OPKG_ASSETSDIR)/CONTROL/control > $(RELEASEDIR)/CONTROL/control
	@echo 2.0 > $(RELEASEDIR)/debian-binary
	@tar --owner=0 --group=0 -czvf $(RELEASEDIR)/data.tar.gz -C $(RELEASEDIR)/data/ . >/dev/null 2>&1
	@tar --owner=0 --group=0 -czvf $(RELEASEDIR)/control.tar.gz -C $(RELEASEDIR)/CONTROL/ . >/dev/null 2>&1
	@ar r $(TARGET).ipk $(RELEASEDIR)/control.tar.gz $(RELEASEDIR)/data.tar.gz $(RELEASEDIR)/debian-binary
	@rm -rf $(RELEASEDIR)

clean:
	rm -f *.o $(BIN)
	rm -rf $(RELEASEDIR)
	rm -f *.ipk
	rm -f *.zip
