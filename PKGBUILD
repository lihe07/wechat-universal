# Maintainer: lihe07 <umikahoshi at gmail dot com>

pkgname=wechat-universal
pkgver=1.0.0.241
pkgrel=1
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
source=(
	"license.tar.gz"
	"${pkgname}.sh"
	"${pkgname}.desktop"
)

_deb_url_common="https://home-store-packages.uniontech.com/appstore/pool/appstore/c/com.tencent.wechat/com.tencent.wechat_${pkgver}"
_deb_stem="${pkgname}_${pkgver}"

source_x86_64=(
	"${_deb_stem}_x86_64.deb::${_deb_url_common}_amd64.deb"
)

source_aarch64=(
	"${_deb_stem}_aarch64.deb::${_deb_url_common}_arm64.deb"
)

source_loong64=(
	"${_deb_stem}_loong64.deb::${_deb_url_common}_loongarch64.deb"
)

noextract=("${_deb_stem}"_{x86_64,aarch64,loong64}.deb)

sha256sums=(
	'53760079c1a5b58f2fa3d5effe1ed35239590b288841d812229ef4e55b2dbd69'
	'708b77df53a07c22ca621877892bf770158e80ba194db3cc74038c5466fbdb9e'
	'b783b7b0035efb5a0fcb4ddba6446f645a4911e4a9f71475e408a5c87ef04c30'
)

sha256sums_x86_64=(
	'2768a97376f2073bd515ef81d64fa7b20668fa6c11a4177a2ed17fc9b03398a3'
)
sha256sums_aarch64=(
	'e4a0387a4855757a229ffed586e31b1bc5c8971d30eef1c079d6757e704e058a'
)
sha256sums_loong64=(
	'90c3276fd8e338eb50162bcb0eef9a41cb553187851d0d5f360e3d010138c8b9'
)

package() {
	echo 'Popupating pkgdir with data from wechat-universal deb file...'
	bsdtar -xOf "${_deb_stem}_${CARCH}.deb" ./data.tar.xz |
		xz -cdT0 |
		bsdtar -xpC "${pkgdir}" ./opt/apps/com.tencent.wechat
	mv "${pkgdir}"/opt/{apps/com.tencent.wechat/files,"${pkgname}"}

	echo 'Installing icons...'
	for res in 16 32 48 64 128 256; do
		install -Dm644 \
			"${pkgdir}/opt/apps/com.tencent.wechat/entries/icons/hicolor/${res}x${res}/apps/com.tencent.wechat.png" \
			"${pkgdir}/usr/share/icons/hicolor/${res}x${res}/apps/${pkgname}.png"
	done
	rm -rf "${pkgdir}"/opt/apps

	echo 'Fixing licenses...'
	local _wechat_root="${pkgdir}/usr/share/${pkgname}"
	install -dm755 {"${pkgdir}","${_wechat_root}"}/usr/lib/license
	mv "${pkgdir}/opt/${pkgname}/libuosdevicea.so" "${_wechat_root}"/usr/lib/license/
	cp -ra license/etc "${_wechat_root}"
	cp -ra license/var "${_wechat_root}"

	echo 'Installing desktop files...'
	install -Dm644 "${pkgname}.desktop" "${pkgdir}/usr/share/applications/${pkgname}.desktop"
	install -Dm755 "${pkgname}.sh" "${pkgdir}/usr/bin/${pkgname}"
}
