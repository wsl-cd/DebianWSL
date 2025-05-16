OUT_ZIP=Debian.zip
LNCR_EXE=Debian.exe

DLR=curl
DLR_FLAGS=-L
ICO_URL=https://cdn.jsdelivr.net/gh/wsl-cd/DebianWSL@main/res/Logo.ico
BASE_URL=https://salsa.debian.org/debian/WSL/-/raw/master/ARM64/install.tar.gz
ROOTFS_TARBALL_CKSM_URL=${BASE_URL}.sha256sum
LNCR_ZIP_URL=https://github.com/yuk7/wsldl/releases/download/25051300/icons.zip
LNCR_ZIP_EXE=Debian.exe

all: $(OUT_ZIP)

zip: $(OUT_ZIP)
$(OUT_ZIP): ziproot
	@echo -e '\e[1;31mBuilding $(OUT_ZIP)\e[m'
	cd ziproot; bsdtar -a -cf ../$(OUT_ZIP) *

ziproot: Launcher.exe rootfs.tar.gz
	@echo -e '\e[1;31mBuilding ziproot...\e[m'
	mkdir ziproot
	cp Launcher.exe ziproot/${LNCR_EXE}
	cp rootfs.tar.gz ziproot/

exe: Launcher.exe
Launcher.exe: icons.zip
	@echo -e '\e[1;31mExtracting Launcher.exe...\e[m'
	bsdtar -xvf icons.zip $(LNCR_ZIP_EXE)
	mv $(LNCR_ZIP_EXE) Launcher.exe

icons.zip:
	@echo -e '\e[1;31mDownloading icons.zip...\e[m'
	$(DLR) $(DLR_FLAGS) $(LNCR_ZIP_URL) -o icons.zip

rootfs.tar.gz: rootfs
	@echo -e '\e[1;31mBuilding rootfs.tar.gz...\e[m'
	cd rootfs; sudo tar -zcpf ../rootfs.tar.gz `sudo ls`
	sudo chown `id -un` rootfs.tar.gz

rootfs: base.tar.gz wsl.conf
	@echo -e '\e[1;31mBuilding rootfs...\e[m'
	mkdir rootfs
	sudo tar -zxpf base.tar.gz -C rootfs
	sudo cp -f wsl.conf rootfs/etc/wsl.conf
	sudo $(DLR) $(DLR_FLAGS) $(ICO_URL) -o rootfs/usr/lib/wsl/debian_logo.ico
	sudo chmod +x rootfs

base.tar.gz:
	@echo -e '\e[1;31mDownloading base.tar.gz...\e[m'
	$(DLR) $(DLR_FLAGS) $(BASE_URL) -o base.tar.gz
	$(DLR) $(DLR_FLAGS) $(ROOTFS_TARBALL_CKSM_URL) -o base.tar.gz.sha256sum.txt
	if ! (head -c 64 < base.tar.gz.sha256sum.txt; echo "  base.tar.gz") | shasum -a 256 -c; then \
		echo 'Checksum failed.'; \
		rm base.tar.gz base.tar.gz.sha256sum.txt; \
		exit 1; \
	fi

clean:
	@echo -e '\e[1;31mCleaning files...\e[m'
	-rm ${OUT_ZIP}
	-rm -r ziproot
	-rm Launcher.exe
	-rm icons.zip
	-rm rootfs.tar.gz
	-rm rootfs.tar.gz.sha256sum.txt
	-sudo rm -r rootfs
	-rm base.tar.gz
