# Maintainer : Alucryd <alucryd at gmail dot com>

pkgbase=bsnes-aio
pkgname=bsnes-aio
pkgver=091
pkgrel=10
arch=('i686' 'x86_64')
url="http://byuu.org/bsnes/"
license=('GPL3')
source=("http://bsnes.googlecode.com/files/bsnes_v${pkgver}-source.tar.xz" "bsnes.desktop" "bsnes.sh")
sha256sums=('8c85a18ff44195d23b54cad53549152f034ee338e590907e8c8cbc3eaa7daf9f'
            '38e4bcd09abeb54d734bd4f0e71378ee9ece0cac97d5006440ae8b0f263ed5b4'
            'db4c40b80531dfd23056714fbe0e893f43282a4a3e11a3471933a0a8a82c5660')

# UI
_gtk=true
_qt=false
# Profile
_accuracy=false
_compatibility=true
_performance=false


true && pkgname=('bsnes-common' 'bsnes-aio')

if [ "$_gtk" == true ]; then
	[ "$_accuracy" == true ] && pkgname+=('bsnes-gtk-accuracy')
	[ "$_compatibility" == true ] && pkgname+=('bsnes-gtk-compatibility')
	[ "$_performance" == true ] && pkgname+=('bsnes-gtk-performance')
fi

if [ "$_qt" == true ]; then
	[ "$_accuracy" == true ] && pkgname+=('bsnes-qt-accuracy')
	[ "$_compatibility" == true ] && pkgname+=('bsnes-qt-compatibility')
	[ "$_performance" == true ] && pkgname+=('bsnes-qt-performance')
fi

buildui() {
	make compiler=gcc platform=x target=$1 phoenix=$2 profile=$3
	if [ "$1" == ethos ]; then
		mv out/bsnes "out/bsnes-$2-$3"
	else
		mv out/laevateinn "out/laevateinn-$2"
	fi
	make clean
}

installui() {
	cd "bsnes_v${pkgver}-source/bsnes"
	install -dm 755 "${pkgdir}/usr/bin"
	install -dm 755 "${pkgdir}/usr/share/applications"
	if [ "$1" == ethos ]; then
		install -m 755 "../../bsnes.sh" "${pkgdir}/usr/bin/bsnes-$2-$3.sh"
		sed -i "s|_ui|$2|" "${pkgdir}/usr/bin/bsnes-$2-$3.sh"	
		sed -i "s|_profile|$3|g" "${pkgdir}/usr/bin/bsnes-$2-$3.sh"
		install -m 755 "out/bsnes-$2-$3" "${pkgdir}/usr/bin/bsnes-$2-$3"
		install -m 644 "../../bsnes.desktop" "${pkgdir}/usr/share/applications/bsnes-$2-$3.desktop"
		sed -i "s|_ui|$2|g" "${pkgdir}/usr/share/applications/bsnes-$2-$3.desktop"
		sed -i "s|_profile|$3|g" "${pkgdir}/usr/share/applications/bsnes-$2-$3.desktop"
	else
		install -m 755 "../../bsnes.sh" "${pkgdir}/usr/bin/laevateinn-$2.sh"
		sed -i 's|bsnes|laevateinn|' "${pkgdir}/usr/bin/laevateinn-$2.sh"
		sed -i "s|_ui|$2|" "${pkgdir}/usr/bin/laevateinn-$2.sh"
		sed -i "s|-_profile||g" "${pkgdir}/usr/bin/laevateinn-$2.sh"
		install -m 755 "out/laevateinn-$2" "${pkgdir}/usr/bin/laevateinn-$2"
		install -m 644 "../../bsnes.desktop" "${pkgdir}/usr/share/applications/laevateinn-$2.desktop"
		sed -i 's|bsnes|laevateinn|g' "${pkgdir}/usr/share/applications/laevateinn-$2.desktop"
		sed -i "s|_ui|$2|g" "${pkgdir}/usr/share/applications/laevateinn-$2.desktop"
		sed -i "s|-_profile||g" "${pkgdir}/usr/share/applications/laevateinn-$2.desktop"
		ln -s /usr/share/bsnes "${pkgdir}/usr/share/laevateinn"
	fi
}

build() {
	cd "${srcdir}/bsnes_v${pkgver}-source/bsnes"

# QT 4.8.0 fix
	moc -i -Iphoenix/qt/ -o phoenix/qt/platform.moc phoenix/qt/platform.moc.hpp

# Compile bsnes GTK GUI
	if [ "$_gtk" == true ]; then
		msg "Building GTK GUI"
		if [ "$_accuracy" == true ]; then
			buildui ethos gtk accuracy
		fi
		if [ "$_compatibility" == true ]; then
			buildui ethos gtk compatibility
		fi
		if [ "$_performance" == true ]; then
			buildui ethos gtk performance
		fi
	fi

# Compile bsnes QT GUI
	if [ "$_qt" == true ]; then
		msg "Building QT GUI"
		if [ "$_accuracy" == true ]; then
			buildui ethos qt accuracy
		fi
		if [ "$_compatibility" == true ]; then
			buildui ethos qt compatibility
		fi
		if [ "$_performance" == true ]; then
			buildui ethos qt performance
		fi
	fi

# Compile laevateinn GTK GUI
#	if [ "$_gtk" == true ]; then
#		buildui laevateinn gtk accuracy
#	fi

# Compile laevateinn QT GUI
#	if [ "$_qt" == true ]; then
#		buildui laevateinn qt accuracy
#	fi
}

package_bsnes-common() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - Common files"
	arch=('any')

	cd "${srcdir}/bsnes_v${pkgver}-source/bsnes"
	mkdir -p "${pkgdir}/usr/share/bsnes"
	cp -R profile/* "${pkgdir}/usr/share/bsnes/"
	cp data/cheats.xml "${pkgdir}/usr/share/bsnes/"

	mkdir "${pkgdir}/usr/share/pixmaps"
	cp data/bsnes.png "${pkgdir}/usr/share/pixmaps/"

	cd "${srcdir}/bsnes_v${pkgver}-source/shaders"
	mkdir "${pkgdir}/usr/share/bsnes/Video Shaders"
	cp *.shader "${pkgdir}/usr/share/bsnes/Video Shaders/"

	find "${pkgdir}" -type d -print0 | xargs -0 chmod 755
	find "${pkgdir}" -type f -print0 | xargs -0 chmod 644
}

package_bsnes-gtk-accuracy() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - GTK GUI - accuracy profile"
	depends=("bsnes-common>=${pkgver}" 'gtk2' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=("bsnes-ui=${pkgver}-${pkgrel}")
	conflicts=('bsnes-gtk')

	installui ethos gtk accuracy
}

package_bsnes-gtk-compatibility() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - GTK GUI - compatibility profile"
	depends=("bsnes-common>=${pkgver}" 'gtk2' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=("bsnes-ui=${pkgver}-${pkgrel}")
	conflicts=('bsnes-gtk')

	installui ethos gtk compatibility
}

package_bsnes-gtk-performance() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - GTK GUI - performance profile"
	depends=("bsnes-common>=${pkgver}" 'gtk2' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=("bsnes-ui=${pkgver}-${pkgrel}")
	conflicts=('bsnes-gtk')

	installui ethos gtk performance
}

package_bsnes-qt-accuracy() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - QT GUI - accuracy profile"
	depends=("bsnes-common>=${pkgver}" 'qt>=4.7.0' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=("bsnes-ui=${pkgver}-${pkgrel}")
	conflicts=('bsnes-qt')

	installui ethos qt accuracy
}

package_bsnes-qt-compatibility() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - QT GUI - compatibility profile"
	depends=("bsnes-common>=${pkgver}" 'qt>=4.7.0' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=("bsnes-ui=${pkgver}-${pkgrel}")
	conflicts=('bsnes-qt')

	installui ethos qt compatibility
}

package_bsnes-qt-performance() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - QT GUI - performance profile"
	depends=("bsnes-common>=${pkgver}" 'qt>=4.7.0' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=("bsnes-ui=${pkgver}-${pkgrel}")
	conflicts=('bsnes-qt')

	installui ethos qt performance
}

package_laevateinn-gtk() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - Debugger GTK GUI"
	depends=("bsnes-common>=${pkgver}" 'gtk2' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=('laevateinn')

	installui laevateinn gtk
}

package_laevateinn-qt() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - Debugger QT GUI"
	depends=("bsnes-common>=${pkgver}" 'qt>=4.7.0' 'libpulse' 'libao' 'libgl' 'libxv' 'openal' 'sdl')
	provides=('laevateinn')

	installui laevateinn qt
}

package_bsnes-aio() {
	pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code - Dummy package for AUR helpers"
	depends=("bsnes-ui>=${pkgver}-${pkgrel}")
	provides=("bsnes=${pkgver}-${pkgrel}")
	optdepends=('purify: ROM purifier'
	            'beat: Delta patcher')
}

pkgdesc="SNES emulator focused on accuracy, debugging functionality, and clean code"
depends=()
