# dwm

dwm is a minimalist dynamic window manager for the X Window System developed by Suckless that has influenced the development of several other X window managers, including xmonad and awesome. It is externally similar to wmii, but internally much simpler. dwm is written purely in C for performance and security in addition to simplicity, and lacks any configuration interface besides editing the source code. One of the project's guidelines is that the source code is intended to never exceed 2000 SLOC, and options meant to be user-configurable are all contained in a single header file.

wikipedia.org/wiki/Dwm

![dwm logo](https://upload.wikimedia.org/wikipedia/commons/c/c7/Dwm-logo.png)

## How to use this Makejail

```
OPTION x11
OPTION copydir=files
OPTION file=/etc/rc.conf.local
OPTION file=/dwm/config.def.h.diff
OPTION file=/dwm/dwm-alpha-20230401-348f655.diff

INCLUDE options/network.makejail
INCLUDE gh+AppJail-makejails/dwm

START

# Patches

# Patch: config.def.h
CMD cd /dwm; patch -i config.def.h.diff
CMD rm -f /dwm/config.def.h.diff
# Patch: dwm-alpha-20230401-348f655.diff
CMD cd /dwm; patch -i dwm-alpha-20230401-348f655.diff
CMD rm -f /dwm/dwm-alpha-20230401-348f655.diff

# Font
PKG liberation-fonts-ttf

STOP
```

Where `options/network.makejail` are the options that suit your environment, for example:

```
ARG network?
ARG interface=dwm

OPTION virtualnet=${network}:${interface} default
OPTION nat
```

The tree structure of the `files/` directory is as follows:

```
# tree -pug files/
[drwxr-xr-x root     wheel   ]  files/
├── [drwxr-xr-x root     wheel   ]  dwm
│   ├── [-rw-r--r-- root     wheel   ]  config.def.h.diff
│   └── [-rw-r--r-- root     wheel   ]  dwm-alpha-20230401-348f655.diff
└── [drwxr-xr-x root     wheel   ]  etc
    └── [-rw-r--r-- root     wheel   ]  rc.conf.local

3 directories, 3 files
```

Open a shell and run `appjail makejail`:

```
appjail makejail -j dwm
```

dwm can be built using the `dwm_build` custom stage:

```
appjail run -s dwm_build dwm
```

dwm can be executed using the `dwm_open` custom stage. xephyr is very useful for using these applications:

```
Xephyr -screen 900x640 -br -ac -noreset :1 &
appjail run -s dwm_open -p display=:1 dwm
```

If you need to get the files after compilation, use the `destdir` argument to a mounted host directory:

```
# mkdir work
# appjail cmd jexec dwm mkdir -p /work
# appjail fstab jail dwm set -d "${PWD}/work" -m /work
# appjail fstab jail dwm compile
# appjail fstab jail dwm mount -a
# appjail run -s dwm_build -p destdir=/work dwm
...
# tree -pug work/
[drwxr-xr-x root     wheel   ]  work/
└── [drwxr-xr-x root     wheel   ]  usr
    └── [drwxr-xr-x root     wheel   ]  local
        ├── [drwxr-xr-x root     wheel   ]  bin
        │   └── [-rwxr-xr-x root     wheel   ]  dwm
        └── [drwxr-xr-x root     wheel   ]  share
            └── [drwxr-xr-x root     wheel   ]  man
                └── [drwxr-xr-x root     wheel   ]  man1
                    └── [-rw-r--r-- root     wheel   ]  dwm.1

7 directories, 2 files
```

## See also

* [dwm patches](https://dwm.suckless.org/patches).
