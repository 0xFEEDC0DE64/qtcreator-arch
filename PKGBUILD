# Maintainer: Rodrigo Bezerra <rodrigobezerra21 at gmail dot com>
# Contributor: delor <bartekpiech@gmail com>
# Contributor: Lukas Jirkovsky <l.jirkovsky@gmail.com>
# Contributor: Dan Vratil <progdan@progdansoft.com>
# Contributor: thotypous <matiasΘarchlinux-br·org>
# Contributor: Imanol Celaya <ornitorrincos@archlinux-es.org>
# Contributor: heinz from #qt-creator
# Contributor: Tobias Hunger <tobias dot hunger at gmail dot com>

pkgname=qtcreator-git
pkgver=v4.9.0.r473.g1de548d1cb
pkgrel=1
pkgdesc='Lightweight, cross-platform integrated development environment'
arch=('x86_64')
url='https://www.qt.io'
license=(LGPL)
depends=(clang clazy qt5-quickcontrols qt5-quickcontrols2 qt5-script qt5-tools qt5-webengine)
makedepends=(git llvm mesa python)
options=(docs)
optdepends=('bzr: bazaar support'
            'cmake: cmake project support'
            'cvs: cvs support'
            'gdb: debugger'
            'git: git support'
            'mercurial: mercurial support'
            'qt5-doc: integrated Qt documentation'
            'qt5-examples: welcome page examples'
            'qt5-translations: other languages'
            'subversion: subversion support'
            'valgrind: analyze support'
            'x11-ssh-askpass: ssh support')
provides=(qtcreator qbs)
conflicts=(qtcreator qbs)
source=('git+https://code.qt.io/qt-creator/qt-creator.git'
        'git+https://code.qt.io/qbs/qbs.git'
        'qtcreator-clang-plugins.patch')
sha256sums=('SKIP'
            'SKIP'
            '26382c282c36aa6716af2deadae864ed21ffba0dd568f9b8f8f8048ce304f436')

pkgver() {
    cd qt-creator

    git describe --long | sed -r 's/([^-]*-g)/r\1/;s/-/./g'
}

prepare() {
    mkdir -p build

    cd qt-creator

    # fix hardcoded libexec path
    sed -e 's|libexec\/qtcreator|lib\/qtcreator|g' -i qtcreator.pri

    # Load analyzer plugins on demand, since upstream clang doesn't link to all plugins
    # see http://code.qt.io/cgit/clang/clang.git/commit/?id=7f349701d3ea0c47be3a43e265699dddd3fd55cf
    # and https://bugs.archlinux.org/task/59492
    patch -p1 -i ../qtcreator-clang-plugins.patch

    # Do *NOT* use system Qbs: qt creator master is *NOT* compatible with any released Qbs!
    ( cd src/shared && rm -rf qbs && ln -s ../../../qbs qbs )
}

build() {
    cd build

    qmake LLVM_INSTALL_DIR=/usr CONFIG+=journald QMAKE_CFLAGS_ISYSTEM=-I \
        "${srcdir}/qt-creator/qtcreator.pro"

    make
    make docs
}

package() {
    cd build

    make INSTALL_ROOT="${pkgdir}/usr/" install
    make INSTALL_ROOT="${pkgdir}/usr/" install_docs

    install -Dm644 "${srcdir}/qt-creator/LICENSE.GPL3-EXCEPT" \
        "${pkgdir}/usr/share/licenses/qtcreator/LICENSE.GPL3-EXCEPT"
}
