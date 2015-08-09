## Package Signing

Create a PGP key:

```console
$ gpg --gen-key

Please select what kind of key you want:
   (1) RSA and RSA (default)
   (2) DSA and Elgamal
   (3) DSA (sign only)
   (4) RSA (sign only)
Your selection? 1

RSA keys may be between 1024 and 4096 bits long.
What keysize do you want? (2048) 4096

Please specify how long the key should be valid.
         0 = key does not expire
      <n>  = key expires in n days
      <n>w = key expires in n weeks
      <n>m = key expires in n months
      <n>y = key expires in n years
Key is valid for? (0) 0
Key does not expire at all
Is this correct? (y/N) y

GnuPG needs to construct a user ID to identify your key.
Real name: My Package Key
Email address: pkgsrc@example.com
Comment:
You selected this USER-ID:
    "My Package Key <pkgsrc@example.com>"

Change (N)ame, (C)omment, (E)mail or (O)kay/(Q)uit? O

You need a Passphrase to protect your secret key.

gpg: key DEADBEEF marked as ultimately trusted
```

Configure `.gnupg/gpg-agent.conf`:

```conf
daemon
use-standard-socket
max-cache-ttl 315360000
default-cache-ttl 315360000
pinentry-program /path/to/pinentry-tty
```

Configure `.gnupg/gpg.conf`:

```conf
lock-never
no-tty
keyserver hkp://keys.gnupg.net
```

Launch the agent and cache the password

```console
$ gpg-agent
$ echo "hi" >f
$ gpg --sign f

: Test that subsequent runs do not prompt for the password
$ rm -f f.gpg
$ gpg --sign f

: Check the signature
$ gpg --verify f.gpg

$ rm -f f{,.gpg}
```

Add to `mk.conf.local`:

```make
SIGN_PACKAGES=	gpg
```

Add to `pkgbuild.conf.local`

```bash
PKGSRC_GPG_SIGN_AS=DEADBEEF
```
