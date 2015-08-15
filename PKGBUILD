# $Id: PKGBUILD 82062 2010-06-08 11:58:32Z winebar $
# Maintainer: winebar <vince.89@live.it>

pkgname=fixed_qt-appmenu
pkgver=4.7.3
_pkgver=4.7.3
pkgrel=1
pkgdesc='A cross-platform application and UI framework'
arch=('i686' 'x86_64')
url='http://qt.nokia.com/'
license=('GPL3' 'LGPL')
depends=('libpng' 'fontconfig' 'libtiff' 'libmng' 'sqlite3' 'xdg-utils' 'ca-certificates'
         'hicolor-icon-theme' 'alsa-lib' 'glib2' 'dbus' 'libxrender' 'libgl' 'libsm')
optdepends=('postgresql-libs' 'libmysqlclient' 'unixodbc')
makedepends=('mesa' 'inputproto' 'postgresql-libs' 'mysql' 'unixodbc' 'cups' 'libxfixes' 'gtk2')
provides=("qt=${pkgver}")
conflicts=('qt')
install=qt.install
options=('!libtool')
_pkgfqn="qt-everywhere-opensource-src-${_pkgver}"
source=("ftp://ftp.qt.nokia.com/qt/source/${_pkgfqn}.tar.gz"
        'assistant.desktop' 'designer.desktop' 'linguist.desktop' 'qtconfig.desktop'
        'appmenu.patch')
md5sums=('49b96eefb1224cc529af6fe5608654fe'
         'a445c6917086d80f1cfc1e40cb6b0132'
         'd457f0a0ad68a3861c3cadefe3b42ded'
         '668331d9798a0e2b94381efb7be4c513'
         'c29f2993d6a0f73d756d2fa36e130e1c'
	 '7ca519c824d67cfae0281ec50af24ad8')

build() {
	unset QMAKESPEC
	export QT4DIR=$srcdir/$_pkgfqn
	export PATH=${QT4DIR}/bin:${PATH}
	export LD_LIBRARY_PATH=${QT4DIR}/lib:${LD_LIBRARY_PATH}

	cd ${srcdir}/${_pkgfqn}

	# see http://qt.gitorious.org/qt/agateau-qt
	# apply appmenu patch from Aurelien Gateau
	patch -Np1 -i ${srcdir}/appmenu.patch
	cp ${srcdir}/${_pkgfqn}/src/gui/widgets/qabstractmenubarimpl_p.h ${srcdir}/${_pkgfqn}/include/QtGui/private/

	sed -i "s|-O2|$CXXFLAGS|" mkspecs/common/g++.conf
	sed -i "/^QMAKE_RPATH/s| -Wl,-rpath,||g" mkspecs/common/g++.conf
	sed -i "/^QMAKE_LFLAGS\s/s|+=|+= $LDFLAGS|g" mkspecs/common/g++.conf

	./configure -confirm-license -opensource \
		-prefix /usr \
		-sysconfdir /etc \
		-plugindir /usr/lib/qt/plugins \
		-importdir /usr/lib/qt/imports \
		-translationdir /usr/share/qt/translations \
		-datadir /usr/share/qt \
		-docdir /usr/share/doc/qt \
		-examplesdir /usr/share/doc/qt/examples \
		-demosdir /usr/share/doc/qt/demos \
		-largefile \
		-plugin-sql-{psql,mysql,sqlite,odbc} \
		-system-sqlite \
		-xmlpatterns \
		-no-phonon \
		-no-phonon-backend \
		-svg \
		-webkit \
                -script \
		-scripttools \
		-system-zlib \
		-system-libtiff \
		-system-libpng \
		-system-libmng \
		-system-libjpeg \
		-openssl-linked \
		-nomake demos \
		-nomake examples \
		-nomake docs \
		-no-rpath \
		-silent \
		-optimized-qmake \
		-dbus \
		-no-separate-debug-info \
		-reduce-relocations \
		-gtkstyle \
		-opengl \
		-graphicssystem raster \
		-no-openvg \
		-glib
	make
}

package() {
	cd ${srcdir}/${_pkgfqn}
	make INSTALL_ROOT=${pkgdir} install

	# install missing icons and desktop files
	for icon in tools/linguist/linguist/images/icons/linguist-*-32.png ; do
		size=$(echo $(basename ${icon}) | cut -d- -f2)
		install -p -D -m644 ${icon} ${pkgdir}/usr/share/icons/hicolor/${size}x${size}/apps/linguist.png
	done
	install -p -D -m644 src/gui/dialogs/images/qtlogo-64.png ${pkgdir}/usr/share/icons/hicolor/64x64/apps/qtlogo.png
	install -p -D -m644 tools/assistant/tools/assistant/images/assistant.png ${pkgdir}/usr/share/icons/hicolor/32x32/apps/assistant.png
	install -p -D -m644 tools/designer/src/designer/images/designer.png ${pkgdir}/usr/share/icons/hicolor/128x128/apps/designer.png
	install -d ${pkgdir}/usr/share/applications
	install -m644 ${srcdir}/{linguist,designer,assistant,qtconfig}.desktop ${pkgdir}/usr/share/applications/

	# install license addition
	install -D -m644 LGPL_EXCEPTION.txt ${pkgdir}/usr/share/licenses/qt/LGPL_EXCEPTION.txt

	# Fix wrong path in pkgconfig files
	find ${pkgdir}/usr/lib/pkgconfig -type f -name '*.pc' \
		-exec perl -pi -e "s, -L${srcdir}/?\S+,,g" {} \;
	# Fix wrong path in prl files
	find ${pkgdir}/usr/lib -type f -name '*.prl' \
		-exec sed -i -e '/^QMAKE_PRL_BUILD_DIR/d;s/\(QMAKE_PRL_LIBS =\).*/\1/' {} \;
	## Remove file included in qtwebkit package
	#rm ${pkgdir}/usr/share/qt/mkspecs/modules/qt_webkit_version.pri
}
