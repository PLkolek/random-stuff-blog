---
title: "Devil Daggers on Linux"
date: 2019-09-18T20:31:47+02:00
draft: true
---
[Devil Daggers](https://devildaggers.com/) is available on Linux. You might have purchased it, expecting it to run flawlessy, as I did on [gog.com](https://www.gog.com/). Surprise, surprise:

```
> ./start.sh
Running Devil Daggers
./devildaggers: /lib/x86_64-linux-gnu/libcurl.so.4: version `CURL_OPENSSL_3' not found (required by ./devildaggers)
```

What does it mean? It expresses in a convoluted way, that Devil Daggers was implemented using `libcurl` library, that this library was not statically linked when building the game, but instead it's linked dynamically, from the system. So the linker found the library in `/lib/x86_64-linux-gnu`, but then complained about a version mismatch. After a quick google search, it turns out, that Devil Daggers needs `libcurl3`, whereas Ubuntu since 18.10 ships `libcurl4` instead. If you use a different distribution, you might find `libcurl3` in your repositories, but in Ubuntu it's no longer available.

Fortunately, repositories for Ubuntu 18.04 are still online, so we grab the problematic library on our own. Visit https://packages.ubuntu.com/bionic/amd64/libcurl3/download to download the deb file. Do not install it! Extract it instead, copy the lib from `data.tar.xz/./usr/lib/x86_64-linux-gnu/libcurl.so.4.5.0` into the game directory: `game/lib64` and rename to `libcurl.so.4` (it's the name from the error message).

Trying to run the game again gives now:
```
./start.sh
Running Devil Daggers
./devildaggers: error while loading shared libraries: libssl.so.1.0.0: cannot open shared object file: No such file or directory
```

So we supplied the correct version of `libcurl`, but it depends on an older version of `libssl` than available in our system. Solution? Find `libssl` in the dependencies on [libcurl3 page](https://packages.ubuntu.com/bionic/libcurl3), download `amd64` version and repeat the procedure outlined in the previous paragraph: extract deb file, grab `libssl.so.1.0.0` and `libcrypto.so.1.0.0` (it would complain about this one too) and place it in `game/lib64` (this time no renaming needed).

The game should start now, enjoy!

TLDR:

1. Download [libcurl3](http://de.archive.ubuntu.com/ubuntu/pool/universe/c/curl3/libcurl3_7.58.0-2ubuntu2_amd64.deb) and [libssl](http://security.ubuntu.com/ubuntu/pool/main/o/openssl1.0/libssl1.0.0_1.0.2n-1ubuntu5.3_amd64.deb) from Ubuntu 18.04 repositories.
2. Extract `libcurl.so.4.5.0`, `libssl.so.1.0.0` and `libcrypto.so.1.0.0` from the deb file. They are located in `data.tar.xz/./usr/lib/x86_64-linux-gnu/`.
3. Place those files in `$DEVIL_DAGGERS_INSTALLATION_DIRECTORY/game/lib64`
4. Rename `libcurl.so.4.5.0` to `libcurl.so.4` 
5. Enjoy the game!
