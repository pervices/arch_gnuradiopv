# Maintainer: Carl Smedstad <carsme@archlinux.org>
# Contributor: Kyle Keen <keenerd@gmail.com>
# Contributor: David Runge <dvzrv@archlinux.org>
# Contributor: Dominik Heidler <dheidler@gmail.com>
# Contributor: Jonatan Sastre <jsastreh [ at ] hotmail.com>

pkgbase=gnuradio
pkgname=(
  gnuradio
  gnuradio-companion
)
pkgver=3.10.11.0
pkgrel=5
pkgdesc="Signal processing runtime and signal processing software development toolkit"
arch=(x86_64)
url="https://gnuradio.org"
license=(GPL-3.0-or-later)
depends=(
  codec2
  gcc-libs
  glibc
  gmp
  gsl
  libad9361
  libuhd
  libunwind
  libvolk
  python-click
  python-mako
  python-matplotlib
  python-numpy
  python-pygccxml
  python-pyyaml
  python-pyzmq
  python-scipy
  python-thrift
  sdl12-compat
  soapysdr
  spdlog
  thrift
)
makedepends=(
  alsa-lib
  boost
  cmake
  cppzmq
  fftw
  fmt
  gtk3
  jack
  libiio
  libsndfile
  portaudio
  pybind11
  python-cairo
  python-gobject
  python-lxml
  python-packaging
  python-pyqt5
  python-pyqtgraph
  python-pytest
  qt5-base
  qwt
  xdg-utils
  zeromq
)
checkdepends=(
  python-jsonschema
  python-pytest
  python-setuptools
  xorg-server-xvfb
)
_url=https://github.com/gnuradio/gnuradio
source=(
  "$pkgbase-$pkgver.tar.gz::$_url/archive/v$pkgver/$pkgbase-v$pkgver.tar.gz"
  "$_url/releases/download/v$pkgver/$pkgbase-$pkgver.tar.gz.asc"
  "numpy-2.0.patch::$_url/commit/8fbc5eb4b7214a4cb029ccae97205a85d49bdd48.patch"
  "21-fcd.rules"
)
sha256sums=('9ca658e6c4af9cfe144770757b34ab0edd23f6dcfaa6c5c46a7546233e5ecd29'
            'SKIP'
            '27371395b1cb400bf5fa390bf0e2688107e576b9e1d3aec28a08bed8a087c8b9'
            '928f4e4b5d8d9fab634e119b83ba9db9fb3501b4d5527cceb7bfa595818be81a')
validpgpkeys=(
  'B90DDFAC56989BF62262EB812987C77CBB8ED9B2' # GNU Radio Project (Admin) <admin@gnuradio.org>
  'D74F9F146E7F755783583158B343B2BA293E5174' # Marcus Müller (GNU Radio Maintainer) <mmueller@gnuradio.org>
  '723EC3A2B90533C6B93DFBC8ED797743F7951435' # GNU Radio (Software Signing Key) <info@gnuradio.org>
)

prepare() {
  cd $pkgbase-$pkgver
  patch -Np1 -i "$srcdir/numpy-2.0.patch"
}

build() {
  cd $pkgbase-$pkgver
  local cmake_options=(
    -S . -B build
    -D CMAKE_INSTALL_PREFIX=/usr
    -D CMAKE_BUILD_TYPE=None
    -W no-dev
    -D ENABLE_GR_ANALOG=ON
    -D ENABLE_GR_AUDIO=ON
    -D ENABLE_GR_BLOCKS=ON
    -D ENABLE_GR_BLOCKTOOL=ON
    -D ENABLE_GR_CHANNELS=ON
    -D ENABLE_GR_CTRLPORT=ON
    -D ENABLE_GR_DIGITAL=ON
    -D ENABLE_GR_DTV=ON
    -D ENABLE_GR_FEC=ON
    -D ENABLE_GR_FFT=ON
    -D ENABLE_GR_FILTER=ON
    -D ENABLE_GR_IIO=ON
    -D ENABLE_GR_MODTOOL=ON
    -D ENABLE_GR_NETWORK=ON
    -D ENABLE_GR_PDU=ON
    -D ENABLE_GR_QTGUI=ON
    -D ENABLE_GR_SOAPY=ON
    -D ENABLE_GR_TRELLIS=ON
    -D ENABLE_GR_UHD=ON
    -D ENABLE_GR_UTILS=ON
    -D ENABLE_GR_VIDEO_SDL=ON
    -D ENABLE_GR_VOCODER=ON
    -D ENABLE_GR_WAVELET=ON
    -D ENABLE_GR_ZEROMQ=ON
    -D ENABLE_GRC=ON
    -D ENABLE_POSTINSTALL=OFF
  )
  cmake "${cmake_options[@]}"
  cmake --build build
}

check() {
  cd $pkgbase-$pkgver
  xvfb-run ctest --test-dir build --output-on-failure
}

package_gnuradio() {
  depends+=(
    alsa-lib
    boost-libs
    fftw
    fmt
    jack
    libiio
    libsndfile
    portaudio
    spdlog
    zeromq
  )
  optdepends=(
    'gnuradio-companion: for GUI frontend'
    'python-setuptools: for gr_modtool'
  )
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
    etc/gnuradio/conf.d/00-grc-docs.conf
    etc/gnuradio/conf.d/gnuradio-runtime.conf
    etc/gnuradio/conf.d/gr-audio{,-{alsa,jack,oss,portaudio}}.conf
    etc/gnuradio/conf.d/gr-qtgui.conf
    etc/gnuradio/conf.d/gr_log_default.conf
    etc/gnuradio/conf.d/grc.conf
    etc/gnuradio/conf.d/modtool.conf
  )

  cd $pkgbase-$pkgver
  DESTDIR="$pkgdir" cmake --install build
  install -vDm644 -t "$pkgdir/usr/lib/udev/rules.d/" "$srcdir/21-fcd.rules"

  install -vDm644 -t "$pkgdir/usr/share/applications" grc/scripts/freedesktop/gnuradio-grc.desktop
  install -vDm644 -t "$pkgdir/usr/share/mime/packages" grc/scripts/freedesktop/gnuradio-grc.xml
  install -vDm644 -t "$pkgdir/usr/share/metainfo" grc/scripts/freedesktop/org.gnuradio.grc.metainfo.xml
  for size in 16 24 32 48 64 128 256; do
    install -vDm644 "grc/scripts/freedesktop/grc-icon-$size.png" \
      "$pkgdir/usr/share/icons/hicolor/${size}x${size}/apps/gnuradio-grc.png"
  done
}

package_gnuradio-companion() {
  pkgdesc="GUI frontend for gnuradio and SDR"
  depends=(
    gnuradio
    python-cairo
    python-gobject
    python-lxml
    python-opengl
    python-pyqt5
    python-pyqtgraph
    qt5-base
    qwt
  )
}
