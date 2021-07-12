# pinentry-touchid

Custom GPG pinentry program for macOS that allows using Touch ID for fetching the password from the
macOS keychain.

## How does it work

This program interacts with the `gpg-agent` for providing a password, using the following rules:

- If the password entry for given key cannot be found in the Keychain we fallback to the
  `pinentry-mac` program to get the password. We recommend preventing `pinentry-mac` from storing the
  password: uncheck the <kbd>Save in keychain</kbd> checkbox in the dialog.

- If a password entry is found the user will be shown the Touch ID dialog and upon successful
  authentication the password stored from the keychain will be return back to gpg-agent.

- If a password entry is found but is not "owned" by the `pinentry-touchid` program after the
  successful authentication in the Touch ID dialog a normal password will be shown. This is an extra
  step enforced by the macOS keychain. In this dialog click <kbd>Always allow</kbd> after entering
  the password. This will allow `pinentry-touchid` to access the password entry without the need to
  type the additional password, but still the access to the password will be guarded by Touch ID.

## Installation

- Download the `pinentry-touchid` binary from our Releases page

- Configure the `gpg-agent` to use `pinentry-touchid` as its pinentry program. Add or replace the
  following line to your gpg agent configuration in: `~/.gnupg/gpg-agent.conf`:

```
pinentry-program /usr/local/bin/pinentry-touchid
```

You can replace `/usr/local/bin/pinentry-touchid` with the path where the binary was stored.

## Configuration

We recommend to disable the option to store the password in the macOS Keychain for the default
pinentry-mac program with the following option:

```sh
$ defaults write org.gpgtools.common DisableKeychain -bool yes
```

This will allow `pinentry-touchid` to create and automatically take ownership of the entry in the
Keychain. If an entry already exist in the Keychain you need to always allow `pinentry-touchid` to
access the existing entry.

## Disclaimer

This project does not store the password/pin in the [Secure
Enclave](https://support.apple.com/en-gb/guide/security/sec59b0b31ff/web) of your device, instead
uses the normal Keychain entry from
[pinentry-mac](https://github.com/GPGTools/pinentry/tree/master/macosx) if available, or creates a
new one.