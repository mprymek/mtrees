# mtrees

mtree specification files created from FreeBSD distribution files kernel.txz, base.txz, lib32.txz with given patch level applied.

freebsd-10.1-RELEASE-p5-amd64.mtree - FreeBSD 10.1-RELEASE with patch level 5 applied, amd64 arch

## how to reproduce

```
release=10.1-RELEASE
arch=amd64
# PUT RECENT PATCH LEVEL HERE!
patchlevel=p5

# where to create
dst=/data/mtrees_root
zfs_vol=zroot$dst

zfs create $zfs_vol

fetch -o- ftp://ftp.freebsd.org/pub/FreeBSD/releases/$arch/$release/kernel.txz | tar -xzf- -C $dst
fetch -o- ftp://ftp.freebsd.org/pub/FreeBSD/releases/$arch/$release/base.txz   | tar -xzf- -C $dst
fetch -o- ftp://ftp.freebsd.org/pub/FreeBSD/releases/$arch/$release/lib32.txz  | tar -xzf- -C $dst

zfs snapshot ${zfs_vol}@$release-$arch

freebsd-update -b $dst fetch
freebsd-update -b $dst install

zfs snapshot ${zfs_vol}@$release-$patchlevel-$arch

mtree -cK sha256 -p $dst | mtree -SC -K sha256 >mtrees/freebsd-$release-$patchlevel-$arch.mtree

sha256 mtrees/*.mtree > CHECKSUM.SHA256
```

