# Maintainer: Thomas Baechler <thomas@archlinux.org>
# Contributor: James Rayner <iphitus@gmail.com>

pkgname=nvidia-utils
pkgver=256.35
pkgrel=1
pkgdesc="NVIDIA drivers utilities and libraries."
arch=('i686' 'x86_64')
url="http://www.nvidia.com/"
depends=('xorg-server' 'libxvmc')
optdepends=('gtk2: nvidia-settings' 'pkgconfig: nvidia-xconfig')
conflicts=('libgl')
provides=('libgl')
license=('custom')
install=nvidia.install
options=('!strip')

if [ "$CARCH" = "i686" ]; then
	_arch='x86'
	_pkg="NVIDIA-Linux-${_arch}-${pkgver}"
	source=("ftp://download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
	md5sums=('f6af8917a500de28396a438f3f548c88')
elif [ "$CARCH" = "x86_64" ]; then
	_arch='x86_64'
	_pkg="NVIDIA-Linux-${_arch}-${pkgver}-no-compat32"
	source=("ftp://download.nvidia.com/XFree86/Linux-${_arch}/${pkgver}/${_pkg}.run")
	md5sums=('7b5924f14a4d2326b4d916efdb7852ff')
fi

source[1]='20-nvidia.conf'
md5sums[1]='37e417b2c69e46de1346a653e07027fa'

build() {
	cd $srcdir
	sh ${_pkg}.run --extract-only
}

package() {
	cd $srcdir/${_pkg}

	# X driver
	install -D -m755 nvidia_drv.so $pkgdir/usr/lib/xorg/modules/drivers/nvidia_drv.so
	# GLX extension module for X
	install -D -m755 libglx.so.$pkgver $pkgdir/usr/lib/xorg/modules/extensions/libglx.so.$pkgver
	ln -s libglx.so.$pkgver $pkgdir/usr/lib/xorg/modules/extensions/libglx.so	# X doesn't find glx otherwise
	# OpenGL library
	install -D -m755 libGL.so.$pkgver $pkgdir/usr/lib/libGL.so.$pkgver
	# OpenGL core library
	install -D -m755 libnvidia-glcore.so.$pkgver $pkgdir/usr/lib/libnvidia-glcore.so.$pkgver
	# XvMC
	install -D -m644 libXvMCNVIDIA.a $pkgdir/usr/lib/libXvMCNVIDIA.a
	install -D -m755 libXvMCNVIDIA.so.$pkgver $pkgdir/usr/lib/libXvMCNVIDIA.so.$pkgver
	# VDPAU
	install -D -m755 libvdpau_nvidia.so.$pkgver $pkgdir/usr/lib/vdpau/libvdpau_nvidia.$pkgver
	# CUDA
	install -D -m755 libcuda.so.$pkgver $pkgdir/usr/lib/libcuda.so.$pkgver
	install -D -m644 cuda.h $pkgdir/usr/include/cuda/cuda.h
	install -D -m644 cudaGL.h $pkgdir/usr/include/cuda/cudaGL.h
	install -D -m644 cudaVDPAU.h $pkgdir/usr/include/cuda/cudaVDPAU.h
	# nvidia-tls libraries
	install -D -m755 libnvidia-tls.so.$pkgver $pkgdir/usr/lib/libnvidia-tls.so.$pkgver
	install -D -m755 tls/libnvidia-tls.so.$pkgver $pkgdir/usr/lib/tls/libnvidia-tls.so.$pkgver
	# OpenCL
	install -D -m755 libnvidia-compiler.so.$pkgver $pkgdir/usr/lib/libnvidia-compiler.so.$pkgver
	install -D -m755 libOpenCL.so.1.0.0 $pkgdir/usr/lib/libOpenCL.so.1.0.0
	install -D -m644 cl.h $pkgdir/usr/include/CL/cl.h
	install -D -m644 cl_gl.h $pkgdir/usr/include/CL/cl_gl.h
	install -D -m644 nvidia.icd $pkgdir/etc/OpenCL/vendors/nvidia.icd
	install -D -m644 cl_platform.h $pkgdir/usr/include/CL/cl_platform.h

	install -D -m755 libnvidia-cfg.so.$pkgver $pkgdir/usr/lib/libnvidia-cfg.so.$pkgver

	# create soname links
	for _lib in $(find $pkgdir -name '*.so*'); do
		_soname="$(dirname ${_lib})/$(readelf -d "$_lib" | sed -nr 's/.*Library soname: \[(.*)\].*/\1/p')"
		[ -e "${_soname}" ] || ln -s "$(basename ${_lib})" "${_soname}"
	done


	# nvidia-xconfig
	install -D -m755 nvidia-xconfig $pkgdir/usr/bin/nvidia-xconfig
	install -D -m644 nvidia-xconfig.1.gz $pkgdir/usr/share/man/man1/nvidia-xconfig.1.gz
	# nvidia-settings
	install -D -m755 nvidia-settings $pkgdir/usr/bin/nvidia-settings
	install -D -m644 nvidia-settings.1.gz $pkgdir/usr/share/man/man1/nvidia-settings.1.gz
	install -D -m644 nvidia-settings.desktop $pkgdir/usr/share/applications/nvidia-settings.desktop
	install -D -m644 nvidia-settings.png $pkgdir/usr/share/pixmaps/nvidia-settings.png
	sed -e 's:__UTILS_PATH__:/usr/bin:' -e 's:__PIXMAP_PATH__:/usr/share/pixmaps:' -i $pkgdir/usr/share/applications/nvidia-settings.desktop
	# nvidia-bug-report
	install -D -m755 nvidia-bug-report.sh $pkgdir/usr/bin/nvidia-bug-report.sh
	# nvidia-smi
	install -D -m755 nvidia-smi $pkgdir/usr/bin/nvidia-smi
	install -D -m644 nvidia-smi.1.gz $pkgdir/usr/share/man/man1/nvidia-smi.1.gz


	install -D -m644 LICENSE $pkgdir/usr/share/licenses/nvidia/LICENSE
	ln -s nvidia $pkgdir/usr/share/licenses/nvidia-utils
	install -D -m644 README.txt $pkgdir/usr/share/doc/nvidia/README
	ln -s nvidia $pkgdir/usr/share/doc/nvidia-utils

	# Install xorg.conf.d file for nvidia autodetection in xorg.conf-less configurations
	install -D -m644 $srcdir/20-nvidia.conf $pkgdir/etc/X11/xorg.conf.d/20-nvidia.conf
}
