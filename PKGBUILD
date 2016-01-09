#
# Maintainer: bjin <bjin1990 at gmail dot com>
# Maintainer: Iacopo Isimbaldi <isiachi@rhye.it>
#

pkgbase="zfs-dkms"
pkgname=("zfs-dkms" "zfs-utils")
pkgver=0.6.5.4
pkgrel=1
license=('CDDL')
makedepends=("git")
arch=("i686" "x86_64")
url="http://zfsonlinux.org/"
source=("https://github.com/zfsonlinux/zfs/releases/download/zfs-${pkgver}/zfs-${pkgver}.tar.gz"
        "zfs-utils.bash-completion-r1"
        "zfs-utils.initcpio.install"
        "zfs-utils.initcpio.hook")
sha256sums=('780862ec2301ccace412a324787e9df762cff6046e73e2ac0ebdce9e2bd59b0f'
            'b60214f70ffffb62ffe489cbfabd2e069d14ed2a391fac0e36f914238394b540'
            '1e20071fa61a33874505dae0f2d71bb560f43e7faaea735cbde770ea10c133df'
            '250f1232c464a81cc9c8b8ee05f21d752ebeebbc8614fae1c6d0bc600e816ac1')

build() {
    cd "${srcdir}/zfs-${pkgver}"
    ./autogen.sh

    ./configure --prefix=/usr \
                --sysconfdir=/etc \
                --sbindir=/usr/bin \
                --with-mounthelperdir=/usr/bin \
                --libdir=/usr/lib \
                --datadir=/usr/share \
                --includedir=/usr/include \
                --with-udevdir=/lib/udev \
                --libexecdir=/usr/lib/zfs \
                --with-config=user
    make
}

package_zfs-dkms() {
    pkgdesc="Kernel modules for the Zettabyte File System."
    depends=("spl-dkms=${pkgver}" "zfs-utils=${pkgver}-${pkgrel}" "dkms")
    optdepends=("mkinitcpio-dkms: Generate initramfs with zfs modules automatically")
    provides=("zfs")
    conflicts=("zfs-git" "zfs-lts")
    install=zfs.install

    dkmsdir="${pkgdir}/usr/src/zfs-${pkgver}"
    install -d "${dkmsdir}"

    cd "${srcdir}"
    tar -xzf "zfs-${pkgver}.tar.gz" -C "${dkmsdir}" --strip-components 1

    cd "${dkmsdir}"
    ./autogen.sh
    scripts/dkms.mkconf -v ${pkgver} -f dkms.conf -n zfs
    chmod g-w,o-w -R .
}

package_zfs-utils() {
    pkgdesc="Kernel module support files for the Zettabyte File System."
    conflicts=("zfs-utils-git" "zfs-utils-lts")

    cd "${srcdir}/zfs-${pkgver}"
    make DESTDIR="${pkgdir}" install

    # Remove uneeded files
    rm -r "${pkgdir}"/etc/init.d
    rm -r "${pkgdir}"/usr/lib/dracut

    # move module tree /lib -> /usr/lib
    cp -r "${pkgdir}"/{lib,usr}
    rm -r "${pkgdir}"/lib

    install -D -m644 "${srcdir}"/zfs-utils.initcpio.hook "${pkgdir}"/usr/lib/initcpio/hooks/zfs
    install -D -m644 "${srcdir}"/zfs-utils.initcpio.install "${pkgdir}"/usr/lib/initcpio/install/zfs
    install -D -m644 "${srcdir}"/zfs-utils.bash-completion-r1 "${pkgdir}"/usr/share/bash-completion/completions/zfs
}
