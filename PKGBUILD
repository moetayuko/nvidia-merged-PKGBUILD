# Maintainer: Thomas Baechler <thomas@archlinux.org>
# Contributor: James Rayner <iphitus@gmail.com>
pkgbase=nvidia-utils
pkgname=('nvidia-utils' 'opencl-nvidia')
pkgver=280.13
pkgrel=1
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
license=('custom')
install=nvidia.install
options=('!strip')

if [ "$CARCH" = "i686" ]; then
    _arch='x86'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}"
    source=("http://us.download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('c3992c8cef02b7773a398640d8830e77')
elif [ "$CARCH" = "x86_64" ]; then
    _arch='x86_64'
    _pkg="NVIDIA-Linux-${_arch}-${pkgver}-no-compat32"
    source=("http://us.download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
    md5sums=('05e62b61feb34bf412f83ca1074e1917')
fi

create_links() {
    # create soname links
    for _lib in $(find "${pkgdir}" -name '*.so*'); do
            _soname="$(dirname ${_lib})/$(readelf -d "$_lib" | sed -nr 's/.*Library soname: \[(.*)\].*/\1/p')"
            if [ ! -e "${_soname}" ]; then
                    ln -s "$(basename ${_lib})" "${_soname}"
                    ln -s "$(basename ${_soname})" "${_soname/.[0-9]*/}"
            fi
    done
}

build() {
    cd "${srcdir}"
    sh "${_pkg}.run" --extract-only
}

package_opencl-nvidia() {
    pkgdesc="OpenCL implemention for NVIDIA"
    depends=('libcl')
    optdepends=('opencl-headers: headers necessary for OpenCL development')
    cd "${srcdir}/${_pkg}"

    # OpenCL
    install -D -m644 nvidia.icd "${pkgdir}/etc/OpenCL/vendors/nvidia.icd"
    install -D -m755 libnvidia-compiler.so.${pkgver} "${pkgdir}/usr/lib/libnvidia-compiler.so.${pkgver}"
    # CUDA
    install -D -m755 libcuda.so.${pkgver} "${pkgdir}/usr/lib/libcuda.so.${pkgver}"
    install -D -m755 libnvcuvid.so.${pkgver} "${pkgdir}/usr/lib/libnvcuvid.so.${pkgver}"
    create_links
}

package_nvidia-utils() {
    pkgdesc="NVIDIA drivers utilities and libraries."
    depends=('xorg-server' 'libxvmc' 'opencl-nvidia')
    optdepends=('gtk2: nvidia-settings' 'pkg-config: nvidia-xconfig')
    conflicts=('libgl')
    provides=('libgl')
    cd "${srcdir}/${_pkg}"

    # X driver
    install -D -m755 nvidia_drv.so "${pkgdir}/usr/lib/xorg/modules/drivers/nvidia_drv.so"
    # GLX extension module for X
    install -D -m755 libglx.so.${pkgver} "${pkgdir}/usr/lib/xorg/modules/extensions/libglx.so.${pkgver}"
    ln -s libglx.so.${pkgver} "${pkgdir}/usr/lib/xorg/modules/extensions/libglx.so"	# X doesn't find glx otherwise
    # OpenGL library
    install -D -m755 libGL.so.${pkgver} "${pkgdir}/usr/lib/libGL.so.${pkgver}"
    # OpenGL core library
    install -D -m755 libnvidia-glcore.so.${pkgver} "${pkgdir}/usr/lib/libnvidia-glcore.so.${pkgver}"
    # XvMC
    install -D -m644 libXvMCNVIDIA.a "${pkgdir}/usr/lib/libXvMCNVIDIA.a"
    install -D -m755 libXvMCNVIDIA.so.${pkgver} "${pkgdir}/usr/lib/libXvMCNVIDIA.so.${pkgver}"
    # VDPAU
    install -D -m755 libvdpau_nvidia.so.${pkgver} "${pkgdir}/usr/lib/vdpau/libvdpau_nvidia.so.${pkgver}"
    # nvidia-tls library
    install -D -m755 tls/libnvidia-tls.so.${pkgver} "${pkgdir}/usr/lib/libnvidia-tls.so.${pkgver}"
    install -D -m755 libnvidia-cfg.so.${pkgver} "${pkgdir}/usr/lib/libnvidia-cfg.so.${pkgver}"

    install -D -m755 libnvidia-ml.so.${pkgver} "${pkgdir}/usr/lib/libnvidia-ml.so.${pkgver}"

    # nvidia-xconfig
    install -D -m755 nvidia-xconfig "${pkgdir}/usr/bin/nvidia-xconfig"
    install -D -m644 nvidia-xconfig.1.gz "${pkgdir}/usr/share/man/man1/nvidia-xconfig.1.gz"
    # nvidia-settings
    install -D -m755 nvidia-settings "${pkgdir}/usr/bin/nvidia-settings"
    install -D -m644 nvidia-settings.1.gz "${pkgdir}/usr/share/man/man1/nvidia-settings.1.gz"
    install -D -m644 nvidia-settings.desktop "${pkgdir}/usr/share/applications/nvidia-settings.desktop"
    install -D -m644 nvidia-settings.png "${pkgdir}/usr/share/pixmaps/nvidia-settings.png"
    sed -e 's:__UTILS_PATH__:/usr/bin:' -e 's:__PIXMAP_PATH__:/usr/share/pixmaps:' -i "${pkgdir}/usr/share/applications/nvidia-settings.desktop"
    # nvidia-bug-report
    install -D -m755 nvidia-bug-report.sh "${pkgdir}/usr/bin/nvidia-bug-report.sh"
    # nvidia-smi
    install -D -m755 nvidia-smi "${pkgdir}/usr/bin/nvidia-smi"
    install -D -m644 nvidia-smi.1.gz "${pkgdir}/usr/share/man/man1/nvidia-smi.1.gz"


    install -D -m644 LICENSE "${pkgdir}/usr/share/licenses/nvidia/LICENSE"
    ln -s nvidia "${pkgdir}/usr/share/licenses/nvidia-utils"
    install -D -m644 README.txt "${pkgdir}/usr/share/doc/nvidia/README"
    install -D -m644 NVIDIA_Changelog "${pkgdir}/usr/share/doc/nvidia/NVIDIA_Changelog"
    ln -s nvidia "${pkgdir}/usr/share/doc/nvidia-utils"

    create_links
}
