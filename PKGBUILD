# Maintainer: Ceaser Larry <clarry@divergentlogic.com> -> https://github.com/ceaser/
pkgname=pac-server-git
pkgver=v0.0.3.r0.9e13683
pkgrel=1
pkgdesc="Browser proxy auto-config server"
arch=(x86_64)
url="https://github.com/ceaser/pac-server"
license=('MIT')
makedepends=(git go)
backup=(etc/pac-server.conf var/spool/pac-server/pac.js)
source=('git+https://github.com/ceaser/pac-server.git')
md5sums=('SKIP')

_gitname="pac-server"

pkgver() {
  cd "${srcdir}/github.com/${_gitname}/${_gitname}"
	printf "%s" "$(git describe --long --tags | sed 's/\([^-]*-\)g/r\1/;s/-/./g')"
}

prepare() {
  install -d "${srcdir}/github.com/${_gitname}"
  if [ -d "${srcdir}/github.com/${_gitname}/${_gitname}" ]; then
    rm -rf "${srcdir}/github.com/${_gitname}/${_gitname}"
  fi
  mv pac-server "${srcdir}/github.com/${_gitname}/${_gitname}"
}

build() {
  cd "${srcdir}/github.com/${_gitname}/${_gitname}"
  export GOPATH="${srcdir}" GOROOT_FINAL="/usr/bin"
  make build
  cat > pac-server.conf <<'EOL'
Address=localhost:11297
MaxAge=31536000
PacFile=/var/spool/pac-server/pac.js
TemplatePath=/usr/share/pac-server/tmpl
EOL
  cat > pac-server.service <<'EOL'
[Unit]
Description=Pac Server - Auto Proxy Configuration Server
After=local-fs.target network.target

[Service]
Type=simple
EnvironmentFile=/etc/pac-server.conf
ExecStart=/usr/bin/pac-server -address ${Address} -maxage ${MaxAge} -pacfile ${PacFile} -templatepath ${TemplatePath}
User=http
Group=http

[Install]
WantedBy=multi-user.target
EOL
}

check() {
  cd "${srcdir}/github.com/${_gitname}/${_gitname}"
  go test $(go list ./...)
}

package() {
  cd "${srcdir}/github.com/${_gitname}/${_gitname}"
  install -Dm644 --owner http --group http example.js "${pkgdir}/var/spool/pac-server/pac.js"
  install -Dm644 tmpl/edit.html "${pkgdir}/usr/share/pac-server/tmpl/edit.html"
  install -Dm755 pac-server ${pkgdir}/usr/bin/pac-server
  install -Dm644 pac-server.service "${pkgdir}/usr/lib/systemd/system/pac-server.service"
  install -Dm644 pac-server.conf "${pkgdir}/etc/pac-server.conf"
}
