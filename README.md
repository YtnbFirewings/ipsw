# ipsw

[![Circle CI](https://circleci.com/gh/blacktop/ipsw.png?style=shield)](https://circleci.com/gh/blacktop/ipsw) [![Build status](https://ci.appveyor.com/api/projects/status/jcx0faojt820p5w4?svg=true)](https://ci.appveyor.com/project/blacktop/ipsw)
[![Github All Releases](https://img.shields.io/github/downloads/blacktop/ipsw/total.svg)](https://github.com/blacktop/ipsw/releases/latest) [![GitHub release](https://img.shields.io/github/release/blacktop/ipsw.svg)](https://github.com/blacktop/ipsw/releases) [![License](http://img.shields.io/:license-mit-blue.svg)](http://doge.mit-license.org)

> Download and parse ipsw(s) from [ipsw.me](https://ipsw.me) or [theiphonewiki.com](https://theiphonewiki.com)

---

## Install

### macOS

```bash
$ brew install blacktop/tap/ipsw
```

### linux/windows

Download from [releases](https://github.com/blacktop/ipsw/releases/latest)

## Getting Started

```bash
$ ipsw --help

Usage: ipsw [OPTIONS] COMMAND [arg...]

IPSW Downloader

Version: 18.12.1, BuildTime: 2018-12-04T01:10:58Z
Author:
  blacktop - <https://github.com/blacktop>

Options:
  --verbose, -V  verbose output
  --help, -h     show help
  --version, -v  print the version

Commands:
  diff        diff kernelcache (using assert strings)
  extract     extract and decompress a kernelcache
  decompress  decompress a kernelcache
  download    download and parse ipsw from the internet
  help        Shows a list of commands or help for one command

Run 'ipsw COMMAND --help' for more information on a command.
```

### `download`

#### Download an `ipsw` and extract/decompress the `kernelcache`

```bash
$ ipsw download --device iPhone11,2 --build 16A366 --dec

   • Getting IPSW              build=16A366 device=iPhone11,2 signed=true version=12.0
      3.4 GiB / 3.4 GiB [==========================================================| 00:00 ] 79.08 MiB/s
      • verifying md5sum...
   • Extracting Kernelcache from IPSW
   • Parsing Compressed Kernelcache
      • compressed size: 17842843, uncompressed: 35727352. unknown: 0x3f9543fd, unknown 1: 0x1
   • Decompressing Kernelcache
```

Notice that the `kernelcache` was extracted from the `ipsw` and decompressed :smiling_imp:

```bash
$ file kernelcache.release.iphone11.decompressed

kernelcache.release.iphone11.decompressed: "Mach-O 64-bit executable arm64"
```

#### Download all the iOS 12.0 `ipsws`

```bash
$ ipsw download --iversion 12.0 --dec

? You are about to download 17 ipsw files. Continue? Yes
   • Getting IPSW              build=16A366 device=iPhone11,4 signed=true version=12.0
    3.3 GiB / 3.3 GiB [==========================================================| 00:00 ] 59.03 MiB/s
      • verifying md5sum...
   • Extracting Kernelcache from IPSW
   • Parsing Compressed Kernelcache
      • compressed size: 17842843, uncompressed: 35727352. unknown: 0x3f9543fd, unknown 1: 0x1
   • Decompressing Kernelcache
   • Getting IPSW              build=16A366 device=iPod7,1 signed=true version=12.0
    734.7 MiB / 2.6 GiB [===============>------------------------------------------| 00:57 ] 44.84 MiB/s
  ...
```

#### Only download and decompress the kernelcaches _(which is CRAZY fast)_

Single `kernelcache`

```bash
ipsw download --device iPhone11,2 --build 16B92 --kernel --dec
```

All of dem!!!

```bash
$ time ipsw download --iversion 12.0.1 --kernel --dec

"8.40s user 1.19s system 53% cpu 17.784 total"
```

That's **14** decompressed kernelcaches in under **9 seconds** :smirk:

```bash
$ ls -1

kernelcache.release.ipad4b.decompressed
kernelcache.release.ipad5b.decompressed
kernelcache.release.ipad6b.decompressed
kernelcache.release.ipad6d.decompressed
kernelcache.release.ipad6f.decompressed
kernelcache.release.ipad7.decompressed
kernelcache.release.iphone10b.decompressed
kernelcache.release.iphone11.decompressed
kernelcache.release.iphone11b.decompressed
kernelcache.release.iphone7.decompressed
kernelcache.release.iphone8b.decompressed
kernelcache.release.iphone9.decompressed
kernelcache.release.j42d.decompressed
kernelcache.release.n102.decompressed
```

But, how does it work?? 🤔 With the POWER :muscle: of [partialzip](https://github.com/blacktop/partialzip) !!

#### Download with a Proxy :new:

This will download and decompress the `kernelcache` for an `iPhone XS` running `iOS 12.1` behind a corporate proxy

```bash
$ ipsw download --proxy http://proxy.org:[PORT] --device iPhone11,2 --build 16B92 --kernel --dec
```

To disable cert verification

```bash
$ ipsw download --insecure --device iPhone11,2 --build 16B92 --kernel --dec
```

### `extract`

Extract `kernelcache` from a previously downloaded `ipsw`

```bash
$ ipsw extract iPhone11,2_12.0_16A366_Restore.ipsw
```

Extract `dyld_shared_cache` from a previously downloaded `ipsw` _(only on macOS)_ :new:

```bash
$ ipsw extract --dyld iPhone11,2_12.0_16A366_Restore.ipsw
   • Extracting dyld_shared_cache from IPSW
   • Mounting DMG
   • Extracting /tmp/ios/System/Library/Caches/com.apple.dyld/dyld_shared_cache_arm64e to dyld_shared_cache
   • Unmounting DMG
```

### `decompress`

Decompress a previously extracted `kernelcache`

```bash
$ ipsw decompress kernelcache.release.iphone11
```

### `diff` [WIP] :construction:

I am playing with the idea of `diffing` kernelcaches by creating directory structures of Apple's src from assert strings.

Then you could use `git diff` or something to get a quick **high** level view of what Apple has changed by seeing new files being added or removed as well as seeing the line numbers of the assert strings move around.

```bash
$ ipsw diff kernelcache.release.iphone11
```

You can see an example of what this outputs [HERE](https://github.com/blacktop/ipsw/tree/master/diff/Library/Caches/com.apple.xbs/Sources)

## TODO

- [ ] use https://github.com/gocolly/colly
- [ ] create offline copy of ipsw.me API
- [ ] download simultaniously to decrease total time _(need to limit concurrent downloads and 17+ at a time could be bad)_

## Issues

Find a bug? Want more features? Find something missing in the documentation? Let me know! Please don't hesitate to [file an issue](https://github.com/blacktop/ipsw/issues/new)

## License

MIT Copyright (c) 2018 **blacktop**
