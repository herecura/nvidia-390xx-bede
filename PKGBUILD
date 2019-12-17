# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-390xx-bede
pkgver=390.132
_extramodules=5.4-BEDE-external
_current_linux_version=5.4.4
_next_linux_version=5.5
pkgrel=10
pkgdesc="NVIDIA drivers for linux-bede, 390xx legacy branch"
arch=('x86_64')
url="http://www.nvidia.com/"
makedepends=(
    "linux-bede>=$_current_linux_version"
    "linux-bede-headers>=$_current_linux_version"
    "linux-bede<$_next_linux_version"
    "linux-bede-headers<$_next_linux_version"
    "nvidia-390xx-utils=$pkgver"
    "libglvnd"
)
provides=('nvidia')
license=('custom')
options=(!strip)

source=(
    "http://us.download.nvidia.com/XFree86/Linux-x86_64/$pkgver/NVIDIA-Linux-x86_64-$pkgver-no-compat32.run"
)
sha512sums=('cdd9b826d3ad96f6c255296336a988eb8e67e1916859319f6e19a24c32484ec5dbb00312bc641b62068829c757e301d14d6bcfc6d833ce78db83db862df59bbe')

[[ "$CARCH" == "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
}

build() {
    cd $_folder/kernel
    make SYSSRC=/usr/src/linux-bede module
}

package() {
	depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
        "nvidia-390xx-utils=$pkgver"
    )

    local extradir="/usr/lib/modules/$(</usr/src/linux-bede/version)/extramodules"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-modeset.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia-modeset.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-drm.ko" \
        "${pkgdir}${extradir}/$_pkgname/nvidia-drm.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_folder}/kernel/nvidia-uvm.ko" \
            "${pkgdir}${extradir}/$_pkgname/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    find "${pkgdir}" -name '*.ko' -exec xz {} +
}

