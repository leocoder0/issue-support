## 0.environment

`Linux version 5.11.12-300.el7.aarch64 (root@centos7.9) (gcc (GCC) 8.3.1 20190311 (Red Hat 8.3.1-3), GNU ld version 2.30-55.el7.2) #1 SMP Thu Aug 19 09:02:08 UTC 2021`

`librdkafka.1.9.2.tar.gz`

`proftpd-1.3.7e.tar.gz`



## 1.librdkafka install

```bash
tar -zxvf librdkafka.1.9.2.tar.gz -C /usr/src/

mkdir /usr/local/librdkafka

cd /usr/src/librdkafka-1.9.2

./configure --prefix=/usr/local/librdkafka/

make 

make install
```

```bash
[root@proftpd-74 librdkafka]# ls /usr/local/librdkafka/
include  lib  share
```



## 2.proftpd install

```bash
tar -zxvf proftpd-1.3.7e.tar.gz -C /usr/src/

mkdir /usr/local/proftpd

cd /usr/src/proftpd-1.3.7e
```

Upload `mod_kafka.c` and `mod_kafka.h.in` to the src directory, then copy the files to contrib dir:

```bash
cp mod_kafka.c contrib/
cp mod_kafka.h.in contrib/mod_kafka.h
```

Configure and install:

```bash
./configure  --prefix=/usr/local/proftpd  --sysconfdir=/etc/ --enable-nls --enable-openssl --enable-shadow  --with-modules=mod_kafka  --enable-dso --with-shared=mod_kafka --with-includes=/usr/local/librdkafka/include  --with-libraries=/usr/local/librdkafka/lib

make

make install
```



```bash
mkdir /var/ftp
chmod 777 /var/ftp

mkdri /var/log/ftpd
chmod 755 /var/log/ftpd
```



```bash
useradd proftp -s /sbin/nologin -M
useradd leo -s /sbin/nologin -d /home/leo

ftpasswd  --passwd --file=/usr/local/proftpd/ftpd.passwd --name=leo  --uid=1001  --home=/home/leo  --shell=/sbin/nologin
```



```bash
vim /etc/proftpd.conf


ServerName                     "ProFTPD Default Installation"
ServerType                       standalone
DefaultServer                   on
Port                                   21
UseIPv6                            off
Umask                              022
MaxInstances                  30
User                                  proftp
Group                               proftp
DefaultRoot                     /var/ftp
SystemLog                      /var/log/ftpd/proftpd.log
TransferLog                    /var/log/ftpd/transfer.log
AllowOverwrite              on
RequireValidShell          off
AuthUserFile                  /usr/local/proftpd/ftpd.passwd

<Directory "/var/ftp/*" >
<Limit ALL>
AllowAll
</Limit>
</Directory>

<IfModule mod_dso.c>
  LoadModule mod_kafka.c
</IfModule>

<IfModule mod_kafka.c>
  KafkaEngine on
  KafkaLog /var/log/ftpd/kafka.log
  KafkaBroker 192.168.118.71:9092

  LogFormat kafka "%h %l %u %t \"%r\" %s %b"
  KafkaLogOnEvent ALL kafka first-topic
</IfModule>
```



`sbin/proftpd -d10`

```bash
[root@proftpd-74 proftpd]# sbin/proftpd -d10 
2022-08-10 11:23:01,469 proftpd-74 proftpd[10919]: using TCP receive buffer size of 131072 bytes
2022-08-10 11:23:01,469 proftpd-74 proftpd[10919]: using TCP send buffer size of 16384 bytes
2022-08-10 11:23:01,470 proftpd-74 proftpd[10919]: using 'UTF-8' as local charset for UTF-8 conversion
2022-08-10 11:23:01,470 proftpd-74 proftpd[10919]: disabling runtime support for IPv6 connections
2022-08-10 11:23:01,470 proftpd-74 proftpd[10919]: retrieved UID 1000 for user 'proftp'
2022-08-10 11:23:01,470 proftpd-74 proftpd[10919]: retrieved GID 1000 for group 'proftp'
2022-08-10 11:23:01,470 proftpd-74 proftpd[10919]: ROOT PRIVS at mod_auth_file.c:1621
2022-08-10 11:23:01,470 proftpd-74 proftpd[10919]: RELINQUISH PRIVS at mod_auth_file.c:1624
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: <Directory /var/ftp/*>: adding section for resolved path '/var/ftp/*'
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: <IfModule>: using 'mod_dso.c' section at line 23
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: mod_dso/0.5: loading 'mod_kafka.c'
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: mod_dso/0.5: unable to dlopen 'mod_kafka.c': file not found (没有那个文件或目录)
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: mod_dso/0.5: unable to load 'mod_kafka.c'; check to see if '/usr/local/proftpd/libexec/mod_kafka.la' exists
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: mod_dso/0.5: defaulting to 'self' for symbol resolution
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: mod_dso/0.5: unable to find module symbol 'kafka_module' in 'self'
2022-08-10 11:23:01,471 proftpd-74 proftpd[10919]: fatal: LoadModule: error loading module 'mod_kafka.c': 没有那个文件或目录 on line 24 of '/etc/proftpd.conf'

```





**The file tree of proftpd:**

```bash
[root@proftpd-74 proftpd]# tree /usr/local/proftpd/
/usr/local/proftpd/
├── bin
│   ├── ftpasswd
│   ├── ftpcount
│   ├── ftpdctl
│   ├── ftpmail
│   ├── ftpquota
│   ├── ftptop
│   ├── ftpwho
│   └── prxs
├── ftpd.passwd
├── include
│   └── proftpd
│       ├── acconfig.h
│       ├── ascii.h
│       ├── auth.h
│       ├── bindings.h
│       ├── buildstamp.h
│       ├── ccan-json.h
│       ├── child.h
│       ├── class.h
│       ├── cmd.h
│       ├── compat.h
│       ├── conf.h
│       ├── configdb.h
│       ├── config.h
│       ├── ctrls.h
│       ├── data.h
│       ├── default_paths.h
│       ├── dirtree.h
│       ├── display.h
│       ├── encode.h
│       ├── env.h
│       ├── error.h
│       ├── event.h
│       ├── expr.h
│       ├── feat.h
│       ├── filter.h
│       ├── fsio.h
│       ├── ftp.h
│       ├── glibc-glob.h
│       ├── hanson-tpl.h
│       ├── help.h
│       ├── ident.h
│       ├── inet.h
│       ├── jot.h
│       ├── json.h
│       ├── lastlog.h
│       ├── libsupp.h
│       ├── logfmt.h
│       ├── log.h
│       ├── memcache.h
│       ├── mkhome.h
│       ├── mod_ctrls.h
│       ├── mod_kafka.h
│       ├── modules.h
│       ├── netacl.h
│       ├── netaddr.h
│       ├── netio.h
│       ├── openbsd-blowfish.h
│       ├── options.h
│       ├── os.h
│       ├── parser.h
│       ├── pidfile.h
│       ├── pool.h
│       ├── privs.h
│       ├── proctitle.h
│       ├── proftpd.h
│       ├── pr-syslog.h
│       ├── random.h
│       ├── redis.h
│       ├── regexp.h
│       ├── response.h
│       ├── rlimit.h
│       ├── scoreboard.h
│       ├── session.h
│       ├── sets.h
│       ├── signals.h
│       ├── stash.h
│       ├── str.h
│       ├── support.h
│       ├── table.h
│       ├── throttle.h
│       ├── timers.h
│       ├── trace.h
│       ├── utf8.h
│       ├── var.h
│       ├── version.h
│       └── xferlog.h
├── lib
│   ├── pkgconfig
│   │   └── proftpd.pc
│   └── proftpd
├── libexec
│   ├── mod_kafka.a
│   ├── mod_kafka.la
│   └── mod_kafka.so
├── sbin
│   ├── ftpscrub
│   ├── ftpshut
│   ├── in.proftpd -> ./proftpd
│   └── proftpd
├── share
│   ├── locale
│   │   ├── bg_BG
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── en_US
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── es_ES
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── fr_FR
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── it_IT
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── ja_JP
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── ko_KR
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── ru_RU
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   ├── zh_CN
│   │   │   └── LC_MESSAGES
│   │   │       └── proftpd.mo
│   │   └── zh_TW
│   │       └── LC_MESSAGES
│   │           └── proftpd.mo
│   └── man
│       ├── man1
│       │   ├── ftpasswd.1
│       │   ├── ftpcount.1
│       │   ├── ftpmail.1
│       │   ├── ftpquota.1
│       │   ├── ftptop.1
│       │   └── ftpwho.1
│       ├── man5
│       │   ├── proftpd.conf.5
│       │   └── xferlog.5
│       └── man8
│           ├── ftpdctl.8
│           ├── ftpscrub.8
│           ├── ftpshut.8
│           └── proftpd.8
└── var
    ├── proftpd.pid
    ├── proftpd.scoreboard
    └── proftpd.scoreboard.lck

```



**The file tree of librdkafka:**

```bash
/usr/local/librdkafka/
├── include
│   └── librdkafka
│       ├── rdkafkacpp.h
│       ├── rdkafka.h
│       └── rdkafka_mock.h
├── lib
│   ├── librdkafka.a
│   ├── librdkafka++.a
│   ├── librdkafka.so -> librdkafka.so.1
│   ├── librdkafka++.so -> librdkafka++.so.1
│   ├── librdkafka.so.1
│   ├── librdkafka++.so.1
│   ├── librdkafka-static.a
│   └── pkgconfig
│       ├── rdkafka.pc
│       ├── rdkafka++.pc
│       ├── rdkafka-static.pc
│       └── rdkafka++-static.pc
└── share
    └── doc
        └── librdkafka
            ├── CHANGELOG.md
            ├── CONFIGURATION.md
            ├── INTRODUCTION.md
            ├── LICENSE
            ├── LICENSES.txt
            ├── README.md
            └── STATISTICS.md
```

