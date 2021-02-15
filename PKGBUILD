# Maintainer: Det <nimetonmaili g-mail>
# Based on [extra]'s thunderbird

# Get latest
latestbinurl=$(curl -sI "https://download.mozilla.org/?product=thunderbird-beta-latest&os=linux64&lang=en-US" | grep "Location: " | cut -d " " -f2 | tr -d '\r')
basename=$(basename "$latestbinurl")

pkgname=thunderbird-beta-bin
_pkgname=thunderbird-beta
pkgver=86.0b2
_major=${pkgver/rc*}
_build=${pkgver/*rc}
pkgrel=1
pkgdesc="Standalone Mail/News reader - Bleeding edge binary version"
arch=('x86_64')
url="https://www.mozilla.org/thunderbird"
license=('GPL' 'LGPL' 'MPL')
depends=('dbus-glib' 'gtk3' 'libxt' 'nss' 'hunspell')
optdepends=('hyphen: Hyphenation'
            'libcanberra: Sound support')
provides=("thunderbird=$pkgver")
conflicts=('thunderbird-beta')
install=$pkgname.install
source=("$latestbinurl" 
        'thunderbird-beta-bin.desktop'
        'vendor.js')

pkgver() {
  echo "$(echo "$basename" | cut -d "-" -f2 | cut -d "." -f1).$(echo "$basename" | cut -d "-" -f2 | cut -d "." -f2)"
}

sha512sums=($(curl -v --silent https://download-installer.cdn.mozilla.net/pub/thunderbird/releases/$(pkgver)/SHA512SUMS 2>&1 | grep "linux-$arch/en-US/$basename" | cut -d " " -f1) 
            '97976bec26750151ecc474a4d6aabffce5c45de077e2c3c4b76035ae282a26a6959c8dc4726227bb2243c98ff81d92a5eeb252eb234ad702ae4cd4263a630c73'
            'aeb444784732267f1b1e87e6084a776f82a1912c4c2637d2cf1de1c135dd9d41d2ef66d2bd3f9cbd3a79fad32d17ea6e2968ba644d5f887cb66ba6c09a2098f5')

# RC
if [[ $_build = ? ]]; then
  source[0]="thunderbird-$pkgver.tar.bz2::$latestbinurl"
fi

package() {
  # Create directories
  msg2 "Creating directory structure..."
  install -d "$pkgdir"/usr/bin
  install -d "$pkgdir"/usr/share/applications
  install -d "$pkgdir"/opt

  msg2 "Moving stuff in place..."
  # Install
  cp -r thunderbird/ "$pkgdir"/opt/$_pkgname

  # Launchers
  ln -s /opt/$_pkgname/thunderbird "$pkgdir"/usr/bin/$_pkgname
  # breaks application as of 68.0b1
  # ln -sf thunderbird "$pkgdir"/opt/$_pkgname/thunderbird-bin

  # vendor.js
  _vendorjs="$pkgdir/opt/$_pkgname/defaults/preferences/vendor.js"
  install -Dm644 /dev/stdin "$_vendorjs" <<END
// Use LANG environment variable to choose locale
pref("intl.locale.matchOS", true);

// Disable default mailer checking.
pref("mail.shell.checkDefaultMail", false);

// Don't disable our bundled extensions in the application directory
pref("extensions.autoDisableScopes", 11);
pref("extensions.shownSelectionUI", true);
END

  # Desktop
  install -m644 $pkgname.desktop "$pkgdir"/usr/share/applications/

  # Icons
  for i in 16 22 24 32 48 256; do
    install -d "$pkgdir"/usr/share/icons/hicolor/${i}x${i}/apps/
    ln -s /opt/$_pkgname/chrome/icons/default/default$i.png \
          "$pkgdir"/usr/share/icons/hicolor/${i}x${i}/apps/$_pkgname.png
  done

  # Use system-provided dictionaries
  ln -Ts /usr/share/hunspell "$pkgdir"/opt/$_pkgname/dictionaries
  ln -Ts /usr/share/hyphen "$pkgdir"/opt/$_pkgname/hyphenation

  # Use system certificates
  ln -sf /usr/lib/libnssckbi.so "$pkgdir"/opt/$_pkgname/libnssckbi.so
}
