# Maintainer: lihe07 <umikahoshi at gmail dot com>

pkgname=wechat-universal
pkgver=1.0.0.242
pkgrel=2
pkgdesc="WeChat (Universal) with namespace sandboxing"
arch=('x86_64' 'aarch64' 'loong64')
url="https://weixin.qq.com"
license=('proprietary')
provides=("${pkgname}")
conflicts=("${pkgname}"{,-bwrap,-privileged})
depends=(
	'alsa-lib'
	'at-spi2-core'
	'bash'
	'util-linux'
	'flatpak-xdg-utils'
	'libxcomposite'
	'libxkbcommon-x11'
	'libxrandr'
	'lsb-release'
	'mesa'
	'nss'
	'openssl-1.1'
	'pango'
	'xcb-util-image'
	'xcb-util-keysyms'
	'xcb-util-renderutil'
	'xcb-util-wm'
	'xdg-desktop-portal'
	'xdg-user-dirs'
)

options=(!strip !debug emptydirs)

_lib_uos='libuosdevicea'

source=(
	"license.tar.gz"
	"${pkgname}.sh"
	"${pkgname}.desktop"
	"${_lib_uos}".{c,Makefile}
)

_rpm_stem="wechat-beta_${pkgver}"
_rpm_url_common="https://mirrors.opencloudos.tech/opencloudos/9.2/extras/x86_64/os/Packages/${_rpm_stem}"
_deb_stem="com.tencent.wechat_1.0.0.241"
_deb_url_common="https://home-store-packages.uniontech.com/appstore/pool/appstore/c/com.tencent.wechat/${_deb_stem}"

source_x86_64=("${_rpm_url_common}_amd64.rpm")
source_aarch64=("${_rpm_url_common}_arm64.rpm")
source_loong64=("${_deb_url_common}_loongarch64.deb")

noextract=("${_rpm_stem}"_{amd,arm}64.rpm "${_deb_stem}"_loongarch64.deb)

sha256sums=(
	'53760079c1a5b58f2fa3d5effe1ed35239590b288841d812229ef4e55b2dbd69'
	'708b77df53a07c22ca621877892bf770158e80ba194db3cc74038c5466fbdb9e'
	'b783b7b0035efb5a0fcb4ddba6446f645a4911e4a9f71475e408a5c87ef04c30'
	'fc3ce9eb8dee3ee149233ebdb844d3733b2b2a8664422d068cf39b7fb08138f8'
	'f05f6f907898740dab9833c1762e56dbc521db3c612dd86d2e2cd4b81eb257bf'
)

sha256sums_x86_64=(
	'ff97d711f3c71cbe86ef93e1d04a681af5c3e95e0188f4b411064ced2819d719'
)
sha256sums_aarch64=(
	'8961d5a61e3006438d140accd88a04f72796cc1c147e048b1751e03e5ce4f4ed'
)
sha256sums_loong64=(
	'90c3276fd8e338eb50162bcb0eef9a41cb553187851d0d5f360e3d010138c8b9'
)

prepare() {
	local _arch=
	case "${CARCH}" in
	loong64)
		_arch=loongarch64
		;;
	*) ;;
	esac
	if [[ "${_arch}" ]]; then
		echo 'Extracting data.tar from deb...'
		bsdtar -xOf "${_deb_stem}_${_arch}.deb" ./data.tar.xz |
			xz -cd >data.tar
	fi
	echo 'Preparing to compile libuosdevica.so...'
	mkdir -p "${_lib_uos}"
	mv "${_lib_uos}"{.c,/}
	mv "${_lib_uos}"{.Makefile,/Makefile}
}

build() {
	cd "${_lib_uos}"
	echo "Building ${_lib_uos}.so stub by Zephyr Lykos..."
	make
}

package() {
	local _arch=
	case "${CARCH}" in
	x86_64)
		_arch=amd64
		;;
	aarch64)
		_arch=arm64
		;;
	esac
	if [[ "${_arch}" ]]; then
		echo 'Popupating pkgdir with data from wechat-universal rpm file...'
		bsdtar -C "${pkgdir}" --no-same-owner -xf "${_rpm_stem}_${_arch}.rpm" ./opt/wechat-beta
		install -DTm644 "${pkgdir}"/opt/wechat-beta/icons/wechat.png "${pkgdir}/usr/share/icons/hicolor/256x256/apps/${pkgname}.png"
		rm -rf "${pkgdir}/opt/wechat-beta/icons"
		mv "${pkgdir}"/opt/{wechat-beta,"${pkgname}"}
	else
		echo 'Popupating pkgdir with data from wechat-universal deb file...'
		tar -C "${pkgdir}" --no-same-owner -xf data.tar ./opt/apps/com.tencent.wechat
		mv "${pkgdir}"/opt/{apps/com.tencent.wechat/files,"${pkgname}"}
		rm "${pkgdir}/opt/${pkgname}/${_lib_uos}.so"

		echo 'Installing icons...'
		for res in 16 32 48 64 128 256; do
			install -Dm644 \
				"${pkgdir}/opt/apps/com.tencent.wechat/entries/icons/hicolor/${res}x${res}/apps/com.tencent.wechat.png" \
				"${pkgdir}/usr/share/icons/hicolor/${res}x${res}/apps/${pkgname}.png"
		done
		rm -rf "${pkgdir}"/opt/apps
	fi

	echo 'Fixing licenses...'
	local _wechat_root="${pkgdir}/usr/share/${pkgname}"
	install -dm755 {"${pkgdir}","${_wechat_root}"}/usr/lib/license
	mv "${_lib_uos}/libuosdevicea.so" "${_wechat_root}"/usr/lib/license/
	cp -ra license/etc "${_wechat_root}"
	cp -ra license/var "${_wechat_root}"

	echo 'Installing desktop files...'
	install -Dm644 "${pkgname}.desktop" "${pkgdir}/usr/share/applications/${pkgname}.desktop"
	install -Dm755 "${pkgname}.sh" "${pkgdir}/usr/bin/${pkgname}"
}
