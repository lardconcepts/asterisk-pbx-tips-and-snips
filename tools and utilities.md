# tools and utilities

## Asterisklint - clean up and check dialplan

https://github.com/ossobv/asterisklint

```
pip3 install asterisklint
cd /etc/asterisk
asterisklint dialplan-check extensions.conf
```

## sngrep - SIP flow viewer

https://github.com/irontec/sngrep/

    apt install autoconf libssl-dev gnutls-dev libncursesw5-dev libpcre-ocaml-dev libncurses5-dev 

Then

```
git clone https://github.com/irontec/sngrep
cd sngrep	
./bootstrap.sh
./configure --with-openssl --enable-unicode --enable-ipv6 --enable-eep
# ./configure # if it fails, install what's needed and re-run ./bootstrap.sh
make
make install    # (as root)
```
