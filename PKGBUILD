# Maintainer: Alexey D. <lq07829icatm@rambler.ru>
# Contributor: Gaetan Bisson <bisson@archlinux.org>
# Contributor: Angel Velasquez <angvp@archlinux.org>
# Contributor: Andrea Scarpino <andrea@archlinux.org>
# Contributor: Damir Perisa <damir.perisa@bluewin.ch>
# Contributor: Ben <ben@benmazer.net>

pkgname=mpd-light
pkgver=0.21.25
_majorver=0.21
pkgrel=1
pkgdesc='Flexible, powerful, server-side application for playing music. Light version without ao, ffmpeg, jack, modplug, pulse, shout, sidplay, soundcloud, wavpack, avahi, smbclient and zziplib support.'
url='https://www.musicpd.org/'
license=('GPL')
arch=('i686' 'x86_64' 'armv6h' 'armv7h')
#depends=('audiofile' 'libmad' 'curl' 'faad2' 'sqlite' 'libmms' 'libid3tag' 'libmpdclient'
         #'icu' 'libupnp' 'libvorbis' 'libnfs' 'libsamplerate' 'libsoxr' 'libgme')
depends=('libmad' 'curl' 'faad2' 'libid3tag')
makedepends=('boost' 'meson' 'python-sphinx')
provides=("mpd=$pkgver")
conflicts=('mpd')
replaces=('mpd')
source=("https://www.musicpd.org/download/mpd/${_majorver}/mpd-${pkgver}.tar.xz"
        'mpd.tmpfile'
        'mpd.conf')
sha512sums=('67e0cbf176d18cd63effab0d12b22bea846458cbaa383ead9078c4b5f2a472dbb1d7308af4d6898691e8864a911c808af5ca2c553d8233323b8aaedfdc7189fc'
            '3608f8b0418aa5527917c35308aeca80357c3cf1834cceeade2eaab7fa736117c0b3143cf225478441ffc533b45ff1e8c5579a2e1aa432a4db5ca4cef2dd04e1'
            'f3eaa25925887ae5df52da0119a77729b5761c175a22117ab15a1636b141f4b159db75dc4e9a52e0d16b2bc4b0f617a4e0838a8d3624f98706beb3387971c660')
backup=('etc/mpd.conf')
install=mpd.install

prepare() {
	cd "${srcdir}/mpd-${pkgver}"

	rm -r build
	install -d build
}

build() {
	cd "${srcdir}/mpd-${pkgver}/build"
	_opts=('-Ddocumentation=true'

	       # networking
	       '-Dtcp=false'
	       '-Dlocal_socket=true'
	       '-Dipv6=disabled'

	       # audio formats
	       '-Ddsd=false'

	       # database
	       '-Dupnp=disabled'
	       '-Ddatabase=false'
	       '-Dlibmpdclient=disabled'

	       # neighbor
	       '-Dneighbor=false'

	       # storage
	       '-Dudisks=disabled'
	       '-Dwebdav=disabled'

	       # playlist
	       '-Dcue=false'

	       # input
	       '-Dsmbclient=disabled'
	       '-Dnfs=disabled'
	       '-Dcdio_paranoia=disabled'
	       '-Dcurl=enabled'
	       '-Dmms=disabled'

	       # cloud
	       '-Dqobuz=disabled'
	       '-Dtidal=disabled'
	       '-Dsoundcloud=disabled'

	       # archive
	       '-Dzzip=disabled'
	       '-Dbzip2=disabled'
	       '-Diso9660=disabled'

	       # tags
	       '-Dchromaprint=disabled' # appears not to be used for anything
	       '-Did3tag=disabled'

	       # decoders
	       '-Dadplug=disabled' # not in an official repo
	       '-Dmodplug=disabled'
	       '-Dsidplay=disabled' # unclear why but disabled in the past
	       '-Dwavpack=disabled'
	       '-Dffmpeg=disabled'
	       '-Daudiofile=disabled'
	       '-Dfaad=enabled'
	       '-Dflac=enabled'
	       '-Dfluidsynth=disabled'
	       '-Dgme=disabled'
	       '-Dmad=enabled'
	       '-Dmikmod=disabled'
	       '-Dmpcdec=disabled'
	       '-Dmpg123=disabled'
	       '-Dopus=disabled'
	       '-Dsndfile=disabled'
	       '-Dtremor=disabled'
	       '-Dvorbis=disabled'
	       '-Dwildmidi=disabled'

	       # encoders
	       '-Dwave_encoder=false'
	       '-Dvorbisenc=disabled'
	       '-Dlame=disabled'
	       '-Dtwolame=disabled'
	       '-Dshine=disabled' # not in an official repo

	       # filters
	       '-Dlibsamplerate=disabled'
	       '-Dsoxr=disabled'

	       # output
	       '-Dfifo=true'
	       '-Dhttpd=false'
	       '-Drecorder=false'
	       '-Doss=disabled'
	       '-Dalsa=disabled'
	       '-Dopenal=disabled'
	       '-Dpulse=enabled'
	       '-Dshout=disabled'
	       '-Dao=disabled'
	       '-Djack=disabled'
	       '-Dpipe=false'
	       '-Dsndio=disabled' # interferes with detection of alsa devices

	       # misc
	       '-Dyajl=disabled'
	       '-Ddbus=disabled'
	       '-Dsqlite=disabled'
	       '-Dexpat=disabled'
	       '-Dicu=disabled'
	       '-Diconv=disabled'
	       '-Dpcre=disabled'
	       '-Dzlib=disabled'

	       '-Dzeroconf=disabled'
	)
	arch-meson --auto-features auto .. ${_opts[@]}
	ninja
}

package() {
	cd "${srcdir}/mpd-${pkgver}/build"
	
	DESTDIR="${pkgdir}" ninja install
	install -Dm644 ../doc/mpdconf.example "${pkgdir}"/usr/share/doc/mpd/mpdconf.example
	install -Dm644 ../doc/mpd.conf.5 "${pkgdir}"/usr/share/man/man5/mpd.conf.5
	install -Dm644 ../doc/mpd.1 "${pkgdir}"/usr/share/man/man1/mpd.1

	install -Dm644 "${srcdir}"/mpd.conf "${pkgdir}"/etc/mpd.conf
	install -Dm644 "${srcdir}"/mpd.tmpfile "${pkgdir}"/usr/lib/tmpfiles.d/mpd.conf
	install -d -g 45 -o 45 "${pkgdir}"/var/lib/mpd{,/playlists}

	# Now service file installs only when libsystemd package was found
	if [ -e "${pkgdir}"/usr/lib/systemd/system/mpd.service ]; then 
		sed \
			-e '/\[Service\]/a User=mpd' \
			-e '/WantedBy=/c WantedBy=default.target' \
			-i "${pkgdir}"/usr/lib/systemd/system/mpd.service
	fi
}
