# Maintainer: Carl Smedstad <carsme@archlinux.org>
# Contributor: Kyle Keen <keenerd@gmail.com>
# Contributor: David Runge <dvzrv@archlinux.org>
# Contributor: Dominik Heidler <dheidler@gmail.com>
# Contributor: Jonatan Sastre <jsastreh [ at ] hotmail.com>

pkgbase=gnuradio
pkgname=(
  gnuradio
  gnuradio-companion
  gnuradio-docs
  gnuradio-examples
  gnuradio-utils
  python-gnuradio
)
pkgver=3.10.12.0
pkgrel=10
pkgdesc="Signal processing runtime and signal processing software development toolkit"
arch=(x86_64)
url="https://gnuradio.org"
license=(GPL-3.0-or-later)
makedepends=(
  alsa-lib
  boost
  boost-libs
  cmake
  codec2
  cppzmq
  doxygen
  fftw
  fmt
  gcc-libs
  glibc
  gmp
  gsl
  gtk3
  jack
  libad9361
  libiio
  libsndfile
  libuhd
  libunwind
  libvolk
  mathjax2
  portaudio
  pybind11
  python-cairo
  python-click
  python-gobject
  python-jsonschema
  python-lxml
  python-mako
  python-matplotlib
  python-numpy
  python-packaging
  python-pygccxml
  python-pyqt5
  python-pyqtgraph
  python-pyyaml
  python-pyzmq
  python-scipy
  python-thrift
  qt5-base
  qwt
  sdl12-compat
  soapysdr
  spdlog
  thrift
  xdg-utils
  zeromq
)
checkdepends=(
  python-pytest
  python-setuptools
  xorg-server-xvfb
)
_url=https://github.com/gnuradio/gnuradio
source=(
  "$_url/archive/v$pkgver/$pkgbase-v$pkgver.tar.gz"
  "$_url/releases/download/v$pkgver/$pkgbase-$pkgver.tar.gz.asc"
  "$_url/commit/a166bdf73d3e3bfd362c239bbd58852faaad39c4.patch"
  "21-fcd.rules"
)
sha256sums=('fe78ad9f74c8ebf93d5c8ad6fa2c13236af330f3c67149d91a0647b3dc6f3958'
            'SKIP'
            'fa15d522c024d5a8f97d7e5680b07b3b3a1919cd98914b77c7773606853bf468'
            '928f4e4b5d8d9fab634e119b83ba9db9fb3501b4d5527cceb7bfa595818be81a')
validpgpkeys=(
  'B90DDFAC56989BF62262EB812987C77CBB8ED9B2' # GNU Radio Project (Admin) <admin@gnuradio.org>
  'D74F9F146E7F755783583158B343B2BA293E5174' # Marcus Müller (GNU Radio Maintainer) <mmueller@gnuradio.org>
  '723EC3A2B90533C6B93DFBC8ED797743F7951435' # GNU Radio (Software Signing Key) <info@gnuradio.org>
)

prepare() {
  cd $pkgbase-$pkgver
  # shellcheck disable=SC2016
  sed -i 's/-${DOCVER}//' CMakeLists.txt

  # Strip build path from docs
  sed -i 's/^STRIP_FROM_PATH.*/STRIP_FROM_PATH = @PROJECT_SOURCE_DIR@ @PROJECT_BINARY_DIR@/' \
    docs/doxygen/Doxyfile.in

  # Boost 1.89 compatibility
  patch -Np1 < ../a166bdf73d3e3bfd362c239bbd58852faaad39c4.patch
}

build() {
  cd $pkgbase-$pkgver
  cmake \
    -S . -B build \
    -D CMAKE_INSTALL_PREFIX=/usr \
    -D CMAKE_BUILD_TYPE=None \
    -W no-dev \
    -D ENABLE_POSTINSTALL=OFF
  cmake --build build
}

check() {
  cd $pkgbase-$pkgver
  xvfb-run ctest --test-dir build --output-on-failure
}

_pick() {
  local dest="$1"
  shift
  for obj in "$@"; do
    mkdir -p "$dest/$(dirname "$obj")/"
    mv -v -t "$dest/$(dirname "$obj")/" "$obj"
  done
}

package_gnuradio() {
  depends=(
    alsa-lib
    boost-libs
    codec2
    fftw
    fmt
    gcc-libs
    glibc
    gmp
    gsl
    jack
    libad9361
    libiio
    libsndfile
    libuhd
    libunwind
    libvolk
    portaudio
    python
    python-matplotlib
    python-numpy
    qt5-base
    qwt
    sdl12-compat
    soapysdr
    spdlog
    thrift
    zeromq
  )
  optdepends=('python-setuptools: for gr_modtool')
  provides=(
    libgnuradio-analog.so
    libgnuradio-audio.so
    libgnuradio-blocks.so
    libgnuradio-channels.so
    libgnuradio-digital.so
    libgnuradio-dtv.so
    libgnuradio-fec.so
    libgnuradio-fft.so
    libgnuradio-filter.so
    libgnuradio-iio.so
    libgnuradio-network.so
    libgnuradio-pmt.so
    libgnuradio-qtgui.so
    libgnuradio-runtime.so
    libgnuradio-soapy.so
    libgnuradio-trellis.so
    libgnuradio-uhd.so
    libgnuradio-video-sdl.so
    libgnuradio-vocoder.so
    libgnuradio-wavelet.so
    libgnuradio-zeromq.so
  )
  backup=(
    etc/gnuradio/conf.d/gnuradio-runtime.conf
    etc/gnuradio/conf.d/gr-audio-alsa.conf
    etc/gnuradio/conf.d/gr-audio-jack.conf
    etc/gnuradio/conf.d/gr-audio-oss.conf
    etc/gnuradio/conf.d/gr-audio-portaudio.conf
    etc/gnuradio/conf.d/gr-audio.conf
    etc/gnuradio/conf.d/gr-qtgui.conf
    etc/gnuradio/conf.d/modtool.conf
  )

  cd $pkgbase-$pkgver
  DESTDIR="$pkgdir" cmake --install build
  install -vDm644 -t "$pkgdir/usr/share/doc/$pkgname" ./*.md

  cd "$pkgdir"
  local python_version=$(python -c "import sys; print(sys.version[:4])")
  _pick "$srcdir/gnuradio-companion" \
    "usr/lib/python$python_version/site-packages/gnuradio/grc" \
    etc/gnuradio/conf.d/00-grc-docs.conf \
    etc/gnuradio/conf.d/grc.conf \
    usr/bin/gnuradio-companion \
    usr/bin/grcc \
    usr/share/gnuradio/grc \
    usr/share/man/man1/gnuradio-companion.1 \
    usr/share/man/man1/grcc.1
  _pick "$srcdir/gnuradio-docs" usr/share/doc
  _pick "$srcdir/gnuradio-examples" usr/share/gnuradio/examples
  _pick "$srcdir/gnuradio-utils" \
    usr/bin/gr-ctrlport-monitor \
    usr/bin/gr-perf-monitorx \
    usr/bin/gr_filter_design \
    usr/bin/gr_modtool \
    usr/bin/gr_plot \
    usr/bin/gr_plot_fft \
    usr/bin/gr_plot_psd \
    usr/bin/gr_plot_qt \
    usr/bin/gr_read_file_metadata \
    usr/bin/polar_channel_construction \
    usr/bin/uhd_fft \
    usr/bin/uhd_rx_cfile \
    usr/bin/uhd_rx_nogui \
    usr/bin/uhd_siggen \
    usr/bin/uhd_siggen_gui \
    usr/share/gnuradio/modtool
  _pick "$srcdir/python-gnuradio" "usr/lib/python$python_version"
}

package_gnuradio-companion() {
  pkgdesc+=" (GUI)"
  depends=(
    glib2
    gobject-introspection-runtime
    gtk3
    pango
    python
    python-cairo
    python-gnuradio
    python-gobject
    python-lxml
    python-mako
    python-numpy
    python-qtpy
    python-yaml
  )
  backup=(
    etc/gnuradio/conf.d/00-grc-docs.conf
    etc/gnuradio/conf.d/grc.conf
  )

  cp -va -t "$pkgdir" "$pkgname/"*

  cd $pkgbase-$pkgver
  install -vDm644 -t "$pkgdir/usr/share/applications" \
    grc/scripts/freedesktop/gnuradio-grc.desktop
  install -vDm644 -t "$pkgdir/usr/share/mime/packages" \
    grc/scripts/freedesktop/gnuradio-grc.xml
  install -vDm644 -t "$pkgdir/usr/share/metainfo" \
    grc/scripts/freedesktop/org.gnuradio.grc.metainfo.xml
  for size in 16 24 32 48 64 128 256; do
    install -vDm644 "grc/scripts/freedesktop/grc-icon-$size.png" \
      "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/gnuradio-grc.png"
  done
  install -vDm644 -t "$pkgdir/usr/lib/udev/rules.d" "$srcdir/21-fcd.rules"
}

package_gnuradio-docs() {
  pkgdesc+=" (documentation)"

  cp -va -t "$pkgdir" "$pkgname/"*
}

package_gnuradio-examples() {
  pkgdesc+=" (examples)"
  depends=(
    boost-libs
    fmt
    gcc-libs
    glibc
    gnuradio
    libuhd
    python
    python-gnuradio
    python-matplotlib
    python-numpy
    python-pyqt5
    python-scipy
    qt5-base
    spdlog
  )

  cp -va -t "$pkgdir" "$pkgname/"*
}

package_gnuradio-utils() {
  pkgdesc+=" (utilities)"
  depends=(
    python
    python-gnuradio
    python-matplotlib
    python-numpy
    python-pyqt5
    python-thrift
  )

  cp -va -t "$pkgdir" "$pkgname/"*
}

package_python-gnuradio() {
  pkgdesc+=" (Python module)"
  depends=(
    fmt
    gcc-libs
    glibc
    gmp
    gnuradio
    libuhd
    libvolk
    python
    python-click
    python-mako
    python-matplotlib
    python-numpy
    python-pygccxml
    python-pyqt5
    python-pyqtgraph
    python-pyzmq
    python-scipy
    python-setuptools
    python-thrift
    python-yaml
    qt5-base
    soapysdr
    spdlog
  )

  cp -va -t "$pkgdir" "$pkgname/"*
}
