## 0.environment

`Linux version 5.11.12-300.el7.aarch64 (root@centos7.9) (gcc (GCC) 8.3.1 20190311 (Red Hat 8.3.1-3), GNU ld version 2.30-55.el7.2) #1 SMP Thu Aug 19 09:02:08 UTC 2021`

`librdkafka.1.9.2.tar.gz`

`proftpd-1.3.7e.tar.gz`



## 1.librdkafka install

```bash
tar -zxvf librdkafka.1.9.2.tar.gz -C /usr/src/

mkdir /usr/local/librdkafka

cd /usr/src/librdkafka-1.9.2

./configure

make 

make install
```

```bash
[root@proftpd-74 proftpd]# ll /usr/local/lib
总用量 64616
-rwxr-xr-x. 1 root root 25033518 8月  10 10:15 librdkafka.a
-rwxr-xr-x. 1 root root  4661476 8月  10 10:15 librdkafka++.a
lrwxrwxrwx. 1 root root       15 8月  10 10:15 librdkafka.so -> librdkafka.so.1
lrwxrwxrwx. 1 root root       17 8月  10 10:15 librdkafka++.so -> librdkafka++.so.1
-rwxr-xr-x. 1 root root  9723888 8月  10 10:15 librdkafka.so.1
-rwxr-xr-x. 1 root root  1707544 8月  10 10:15 librdkafka++.so.1
-rwxr-xr-x. 1 root root 25033518 8月  10 10:15 librdkafka-static.a
drwxr-xr-x. 2 root root       96 8月  10 10:15 pkgconfig
[root@proftpd-74 proftpd]# ll /usr/local/include/librdkafka/
总用量 436
-rwxr-xr-x. 1 root root 124770 8月  10 10:15 rdkafkacpp.h
-rwxr-xr-x. 1 root root 307127 8月  10 10:15 rdkafka.h
-rwxr-xr-x. 1 root root  11665 8月  10 10:15 rdkafka_mock.h
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
./configure  --prefix=/usr/local/proftpd  --sysconfdir=/etc/ --enable-nls --enable-openssl --enable-shadow  --with-modules=mod_kafka --with-includes=/usr/local/include/librdkafka/ --with-libraries=/usr/local/lib

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

  LogFormat proftp-log "%h %l %u %t \"%r\" %s %b"
  KafkaLogOnEvent ALL proftp-log
</IfModule>
```



`sbin/proftpd -d10`




