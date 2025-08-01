#---------------------------------------------------------------------------------
.SUFFIXES:
#---------------------------------------------------------------------------------
ifeq ($(strip $(DEVKITARM)),)
$(error "Please set DEVKITARM in your environment. export DEVKITARM=<path to>devkitARM")
endif

include $(DEVKITARM)/ds_rules

export TARGET		:=	bootstrap
export TOPDIR		:=	$(CURDIR)


.PHONY: checkarm7 checkarm9

#---------------------------------------------------------------------------------
# main targets
#---------------------------------------------------------------------------------
all: checkarm7 checkarm9 \
	_ds_menu.dat ismat.dat ez5sys.bin \
	akmenu4.nds TTMENU.DAT SCFW.SC _BOOT_MP.NDS \
	ACE3DS/_ds_menu.dat ACE3DS/_dsmenu.dat EZDS.dat

_ds_menu.dat	:	$(TARGET).nds r4tfv2.dldi
	@cp $(TARGET).nds _ds_menu.nds
	@dlditool r4tfv2.dldi _ds_menu.nds
	@r4denc $< _ds_menu.nds $@

ACE3DS/_ds_menu.dat	:	$(TARGET).nds ace3ds_sd.dldi
	@[ -d ACE3DS ] || mkdir -p ACE3DS
	@cp $(TARGET).nds ACE3DS/_ds_menu.nds
	@dlditool ace3ds_sd.dldi ACE3DS/_ds_menu.nds
	@r4denc --key 0x4002 ACE3DS/_ds_menu.nds $@

# Alternative name for some ACE3DS clones.
ACE3DS/_dsmenu.dat	:	$(TARGET).nds ace3ds_sd.dldi
	@[ -d ACE3DS ] || mkdir -p ACE3DS
	@cp $(TARGET).nds ACE3DS/_dsmenu.nds
	@dlditool ace3ds_sd.dldi ACE3DS/_dsmenu.nds
	@r4denc --key 0x4002 ACE3DS/_dsmenu.nds $@

EZDS.dat: $(TARGET).nds EZDS_DLDI.dldi
	@cp $< $@
	@dlditool EZDS_DLDI.dldi $@

ez5sys.bin	:	$(TARGET).nds EZ5V2.dldi
	@cp $< $@
	@dlditool EZ5V2.dldi $@

akmenu4.nds	:	$(TARGET).nds  ak2_sd.dldi
	@cp $< $@
	@dlditool ak2_sd.dldi $@

TTMENU.DAT	:	$(TARGET).nds  TTCARDIOLibrary.dldi
	@cp $< $@
	@dlditool TTCARDIOLibrary.dldi $@

_BOOT_MP.NDS	:	$(TARGET).nds mpcf.dldi
	@cp $< $@
	@dlditool mpcf.dldi $@

ismat.dat	:	$(TARGET).nds mati.dldi
	@cp $< $@
	@dlditool mati.dldi $@

#---------------------------------------------------------------------------------
# Building separate .dsi and .nds binaries here is done purely to allow the .nds
# binary to be used to replace various flashcart boot menus.
#
# This is *NOT* for general use, standard .nds files with a full header are DSi
# hybrid binaries.
#
#---------------------------------------------------------------------------------
bootstrap.cia: bootstrap.dsi
	make_cia --srl=bootstrap.dsi

#---------------------------------------------------------------------------------
bootstrap.dsi : $(TARGET).arm7.elf $(TARGET).arm9i.elf
	ndstool -u 00030004 -g HBLA -b bootstrap.bmp "nds-hb-menu bootstrap;built with devkitARM;http://devkitpro.org" -c $@ -7 $(TARGET).arm7.elf -9 $(TARGET).arm9i.elf

#---------------------------------------------------------------------------------
$(TARGET).nds	:	$(TARGET).arm7.elf $(TARGET).arm9.elf
	@ndstool	-g "####" "##" "R4XX" -h 0x200 -c $(TARGET).nds -7 $(TARGET).arm7.elf -9 $(TARGET).arm9.elf

SCFW.SC	:	$(TARGET).arm7.elf $(TARGET).arm9.elf
	@ndstool	-g "ENG0" "00" "DSONE" -h 0x200 -c SCFW.SC -7 $(TARGET).arm7.elf -9 $(TARGET).arm9.elf
	@dlditool scdsone_sdhc.dldi $@

checkarm7:
	$(MAKE) -C arm7

checkarm9:
	$(MAKE) -C arm9

#---------------------------------------------------------------------------------
$(TARGET).arm7.elf:
	$(MAKE) -C arm7
	
#---------------------------------------------------------------------------------
$(TARGET).arm9.elf:
	$(MAKE) -C arm9

#---------------------------------------------------------------------------------
$(TARGET).arm9i.elf:
	$(MAKE) -C arm9

#---------------------------------------------------------------------------------
clean:
	$(MAKE) -C arm9 clean
	$(MAKE) -C arm7 clean
	rm -f $(TARGET).nds $(TARGET).arm7.elf $(TARGET).arm9.elf _ds_menu.dat ez5sys.bin akmenu4.nds TTMENU.DAT _BOOT_MP.NDS ismat.dat EZDS.dat
	rm -fr ACE3DS
	rm -f bootstrap.dsi $(TARGET).arm9i.elf bootstrap.cia

