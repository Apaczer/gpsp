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

CHAINPREFIX ?= /opt/miyoo
CROSS_COMPILE ?= $(CHAINPREFIX)/usr/bin/arm-linux-

CC = $(CROSS_COMPILE)gcc
CXX = $(CROSS_COMPILE)g++
STRIP = $(CROSS_COMPILE)strip

# Possible values : 0, YES, APPLY
PROFILE ?= 0

OBJS      = main.o cpu.o memory.o video.o input.o sound.o gui.o \
            cheats.o zip.o arm_stub.o  warm.o cpu_threaded.o video_blend.o sha1.o imageio.o

BIN       = $(TARGET)

# Platform specific definitions

VPATH      += .. ../arm
CFLAGS     = -Ofast -fdata-sections -ffunction-sections -fsingle-precision-constant -fno-PIC -flto
CFLAGS     += -DARM_ARCH -DMIYOO -Wall
CFLAGS     += -march=armv5te -mtune=arm926ej-s $(shell $(SYSROOT)/usr/bin/sdl-config --cflags)
PKG_LIBS   = $(shell $(SYSROOT)/../../usr/bin/pkg-config --libs sdl libpng zlib)
LIBS       = -nodefaultlibs -lc -lgcc $(PKG_LIBS) -Wl,--as-needed -Wl,--gc-sections

ifeq ($(DEBUG), YES)
LIBS       += -g3
endif

ifeq ($(PROFILE), YES)
LIBS       += -lgcov
endif

ifeq ($(PROFILE), YES)
CFLAGS	+= -fprofile-generate=$(HOMEPATH)/profile
else ifeq ($(PROFILE), APPLY)
CFLAGS	+= -fprofile-use -fbranch-probabilities
endif

# Compilation:

.PHONY: config borders

all: $(BIN)

%.o: %.S
	$(CC) $(CFLAGS) -c -o $@ $<

cpu.o cpu_threaded.o: CFLAGS += -Wno-unused-variable -Wno-unused-label

$(BIN): $(OBJS)
	$(CC) $(OBJS) $(LIBS) -o $(BIN) $(LDFLAGS)
ifneq ($(DEBUG), YES)
	$(STRIP) $(BIN)
endif

config:
	@cat ../game_config.txt > $(ASSETSDIR)/game_config.txt

borders:
	@cp ../borders/* $(ASSETSDIR)
#use one of overlays as default border for unscaled image
	@mv $(ASSETSDIR)/border1.png $(ASSETSDIR)/border.png

package: all config borders
	@rm -rf $(RELEASEDIR)
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
	@rm -rf $(RELEASEDIR)/*
	mv $(TARGET)*.zip $(RELEASEDIR)/

ipk: package
	@mkdir -p .$(HOMEPATH)
	@mv $(RELEASEDIR)/* .$(HOMEPATH) && mv .$(HOMEPATH) $(RELEASEDIR)
	@mkdir -p $(RELEASEDIR)/data
	@mv $(RELEASEDIR)$(HOMEPATH) $(RELEASEDIR)/data/
	@echo "#!/bin/sh\nsync; echo 'Installing new $(TARGET)..'; rm /var/lib/opkg/info/$(TARGET).list; exit 0" > $(OPKG_ASSETSDIR)/CONTROL/preinst
	@echo "#!/bin/sh\nsync; echo 'Installation finished.'; echo 'Restarting $(TARGET)..'; sleep 1; killall $(TARGET); exit 0" > $(OPKG_ASSETSDIR)/CONTROL/postinst
	@chmod +x $(OPKG_ASSETSDIR)/CONTROL/postinst $(OPKG_ASSETSDIR)/CONTROL/preinst
	@cp -r $(OPKG_ASSETSDIR)/CONTROL $(RELEASEDIR)
	@sed "s/^Version:.*/Version: $(VERSION)/" $(OPKG_ASSETSDIR)/CONTROL/control > $(RELEASEDIR)/CONTROL/control
	@echo 2.0 > $(RELEASEDIR)/debian-binary
	@tar --owner=0 --group=0 -czvf $(RELEASEDIR)/data.tar.gz -C $(RELEASEDIR)/data/ . >/dev/null 2>&1
	@tar --owner=0 --group=0 -czvf $(RELEASEDIR)/control.tar.gz -C $(RELEASEDIR)/CONTROL/ . >/dev/null 2>&1
	@ar r $(TARGET).ipk $(RELEASEDIR)/control.tar.gz $(RELEASEDIR)/data.tar.gz $(RELEASEDIR)/debian-binary
	@rm -rf $(RELEASEDIR)/*
	mv $(TARGET).ipk $(RELEASEDIR)/

gm2xpkg-ipk: all config borders
	gm2xpkg -i pkg.cfg
	rm $(TARGET).lnk

clean:
	rm -f *.o $(BIN)
	rm -rf $(RELEASEDIR)
	rm -f *.ipk
	rm -f *.zip
