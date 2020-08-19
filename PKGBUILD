# vim:set ft=sh et:
# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-390xx-bede
pkgver=390.138
_current_linux_version=5.8.2
_next_linux_version=5.9
pkgrel=15
pkgdesc="NVIDIA drivers for linux-bede, 390xx legacy branch"
arch=('x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede>=$_current_linux_version"
    "linux-bede-headers>=$_current_linux_version"
    "linux-bede<$_next_linux_version"
    "linux-bede-headers<$_next_linux_version"
    "nvidia-390xx-dkms=$pkgver"
)
provides=('nvidia')
license=('custom')
options=(!strip)

package() {
	depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
        "nvidia-390xx-utils=$pkgver"
        "libglvnd"
    )

    local kernver=$(</usr/src/linux-bede/version)
    local extradir="/usr/lib/modules/$kernver/extramodules"
    # output dkms log for easier debugging
    if [[ -f "/var/lib/dkms/nvidia/${pkgver}/build/make.log" ]]; then
        cat "/var/lib/dkms/nvidia/${pkgver}/build/make.log"
    elif [[ -f "/var/lib/dkms/nvidia/${pkgver}/${kernver}/log/make.log" ]]; then
        cat "/var/lib/dkms/nvidia/${pkgver}/${kernver}/log/make.log"
    fi

    install -dm755 "${pkgdir}${extradir}/$_pkgname"
    cp -a "/var/lib/dkms/$_pkgname/kernel-$kernver-x86_64/module"/* \
        "${pkgdir}${extradir}/$_pkgname/"

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
}












