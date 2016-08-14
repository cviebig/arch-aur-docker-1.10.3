# $Id$
# Maintainer: Sébastien "Seblu" Luttringer

pkgname=docker
pkgver=1.10.3
pkgrel=1
epoch=1
pkgdesc='Pack, ship and run any application as a lightweight container'
arch=('x86_64')
url='https://www.docker.com/'
license=('Apache')
depends=('bridge-utils' 'iproute2' 'device-mapper' 'sqlite' 'systemd')
makedepends=('git' 'go' 'go-md2man')
optdepends=('lxc: lxc backend support')
# don't strip binaries! A sha1 is used to check binary consistency.
options=('!strip')
install=$pkgname.install
source=("git+https://github.com/docker/docker.git#tag=v$pkgver"
        "$pkgname.sysusers")
md5sums=('SKIP'
         '8cf9900ebada61f352a03465a088da34')

prepare() {
  cd docker
  # apply patch from the source array (should be a pacman feature)
  local filename
  for filename in "${source[@]}"; do
    if [[ "$filename" =~ \.patch$ ]]; then
      msg2 "Applying patch $filename"
      patch -p1 -N -i "$srcdir/$filename"
    fi
  done
  :
}

build() {
  cd docker
  export AUTO_GOPATH=1
  export DOCKER_BUILDTAGS='exclude_graphdriver_btrfs'
  hack/make.sh dynbinary
  # man pages
  man/md2man-all.sh
}

#check() {
#  cd docker
#  ./hack/make.sh dyntest
#}

package() {
  cd docker
  install -Dm755 "bundles/$pkgver/dynbinary/docker-$pkgver" "$pkgdir/usr/bin/docker"
  install -Dm755 "bundles/$pkgver/dynbinary/dockerinit-$pkgver" "$pkgdir/usr/lib/docker/dockerinit"
  # completion
  install -Dm644 'contrib/completion/bash/docker' "$pkgdir/usr/share/bash-completion/completions/docker"
  install -Dm644 'contrib/completion/zsh/_docker' "$pkgdir/usr/share/zsh/site-functions/_docker"
  install -Dm644 'contrib/completion/fish/docker.fish' "$pkgdir/usr/share/fish/vendor_completions.d/docker.fish"
  # systemd
  install -Dm644 'contrib/init/systemd/docker.service' "$pkgdir/usr/lib/systemd/system/docker.service"
  install -Dm644 'contrib/init/systemd/docker.socket' "$pkgdir/usr/lib/systemd/system/docker.socket"
  install -Dm644 "$srcdir/$pkgname.sysusers" "$pkgdir/usr/lib/sysusers.d/$pkgname.conf"
  # vim syntax
  install -Dm644 'contrib/syntax/vim/syntax/dockerfile.vim' "$pkgdir/usr/share/vim/vimfiles/syntax/dockerfile.vim"
  install -Dm644 'contrib/syntax/vim/ftdetect/dockerfile.vim' "$pkgdir/usr/share/vim/vimfiles/ftdetect/dockerfile.vim"
  # man
  install -dm755 "$pkgdir/usr/share/man"
  mv man/man* "$pkgdir/usr/share/man"
}

# vim:set ts=2 sw=2 et:
