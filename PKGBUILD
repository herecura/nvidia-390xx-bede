# Maintainer : BlackEagle < ike DOT devolder AT gmail DOT com >
# Contributor: Thomas Baechler <thomas@archlinux.org>

_pkgname=nvidia
pkgname=$_pkgname-390xx-bede
pkgver=390.87
_extramodules=4.20-BEDE-external
_current_linux_version=4.20.6
_next_linux_version=4.21
pkgrel=42
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
    'linux-420-ipmi_user.patch'
    'linux-420-vm_insert_pfn.patch'
)
sha512sums=('91f8639718b8511f56e8c01caafc5a061a3ae1e84202ad261fae94bf83b2c9db8eb5910a9a2b35f668bb3c82dfb3978ca037930a71e396d105c4b4b25c269ed8'
            'cb73f75aa00b130ff3a404aeb7fa875ba664f9567a1a9cd2241a928d4238c3effce53c5db175557a4b93656d0b4909e1141966c47e5fa44e2c5b0442fdc012db'
            'd5ccd06a7ed06d6b504cbee36d6882bb6441cce1bb73cfb08c5dceea19eabbc8e323925db67f28a617627ab5eaac54992796b238ece151f336bce750252b2f4e')

[[ "$CARCH" == "x86_64" ]] && _pkg="NVIDIA-Linux-x86_64-${pkgver}-no-compat32"
#_folder=${_pkg//-no-compat32/}
_folder=${_pkg}

prepare() {
    [ -d "$_folder" ] && rm -rf "$_folder"
    sh $_pkg.run --extract-only
    cd $_folder
    # patch if needed
    sed -i -e 's/drm_mode_connector_attach_encoder/drm_connector_attach_encoder/g' kernel/nvidia-drm/nvidia-drm-encoder.c
    sed -i -e 's/drm_mode_connector_update_edid_property/drm_connector_update_edid_property/g' kernel/nvidia-drm/nvidia-drm-connector.c

    patch -p1 -i "$srcdir/linux-420-ipmi_user.patch"
    patch -p1 -i "$srcdir/linux-420-vm_insert_pfn.patch"
}

build() {
    _kernver="$(cat /usr/lib/modules/$_extramodules/version)"
    cd $_folder/kernel
    make SYSSRC=/usr/lib/modules/$_kernver/build module
}

package() {
	depends=(
        "linux-bede>=$_current_linux_version"
        "linux-bede<$_next_linux_version"
        "nvidia-390xx-utils=$pkgver"
    )

    install -Dm644 "$srcdir/$_folder/kernel/nvidia.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-modeset.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia-modeset.ko"
    install -Dm644 "$srcdir/$_folder/kernel/nvidia-drm.ko" \
        "$pkgdir/usr/lib/modules/$_extramodules/$_pkgname/nvidia-drm.ko"

    if [[ "$CARCH" = "x86_64" ]]; then
        install -D -m644 "${srcdir}/${_folder}/kernel/nvidia-uvm.ko" \
            "${pkgdir}/usr/lib/modules/${_extramodules}/$_pkgname/nvidia-uvm.ko"
    fi

    install -dm755 "$pkgdir/usr/lib/modprobe.d"
    echo "blacklist nouveau" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"
    echo "blacklist nvidiafb" >> "$pkgdir/usr/lib/modprobe.d/$pkgname.conf"

    find "${pkgdir}" -name '*.ko' -exec xz {} +
}

