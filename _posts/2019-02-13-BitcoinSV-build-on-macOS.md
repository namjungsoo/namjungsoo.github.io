---
layout: post
title: "BitcoinSV #0 build on macOS"
---
```
brew install berkeley-db
```

Compiled using the following commands
(most of them are from bitcoin core library)  

```
cd <directory of your choice>
```

Download source  
https://github.com/bitcoin-sv/bitcoin-sv/releases/download/v0.1.0/bitcoin-sv-0.1.0.tar.gz  
```
xcode-select --install  
```

When the popup appears, click Install.

Then install Homebrew.
```
brew install https://raw.githubusercontent.com/Homebrew/homebrew-core/master/Formula/berkeley-db.rb --without-java  
brew install automake libtool boost miniupnpc openssl pkg-config protobuf python qt libevent qrencode  
```

If you want to build the disk image with make deploy (.dmg / optional), you need RSVG:
```
brew install librsvg
```

Build core
```
./autogen.sh
./configure
make 
```

For GUI use (Not functional there is no -qt distribution)
```
./configure --with-gui=qt5
```

if you have already build then:
```
make clean
./autogen.sh
./configure --with-gui=qt5
make
```

It is recommended to build and run the unit tests:
```
make check
```
(not tested)

You can also create a .dmg that contains the .app bundle (optional):
```
make deploy
```

Core is now available at `./src/bitcoind`  

Tested on macOS Mojave 10.14  
8GB RAM  
2.5 GHZ intel i5 x64  

QT is not functional there is no -qt distribution