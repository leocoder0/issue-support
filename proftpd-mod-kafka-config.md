## 0.environment

#### Linux OS:

`Linux version 5.11.12-300.el7.aarch64 (root@centos7.9) (gcc (GCC) 8.3.1 20190311 (Red Hat 8.3.1-3), GNU ld version 2.30-55.el7.2) #1 SMP Thu Aug 19 09:02:08 UTC 2021`

#### Software version:

`librdkafka.1.9.2.tar.gz`

`proftpd-1.3.7e.tar.gz`



## 1.librdkafka installation steps

#### Configure and install

```bash
tar -zxvf librdkafka.1.9.2.tar.gz -C /usr/src/

cd /usr/src/librdkafka-1.9.2

./configure

make 

make install
```

#### Default installation directory:

```bash
[root@proftpd-74 proftpd]# ll /usr/local/lib
-rwxr-xr-x. 1 root root 25033518 8月  10 10:15 librdkafka.a
-rwxr-xr-x. 1 root root  4661476 8月  10 10:15 librdkafka++.a
lrwxrwxrwx. 1 root root       15 8月  10 10:15 librdkafka.so -> librdkafka.so.1
lrwxrwxrwx. 1 root root       17 8月  10 10:15 librdkafka++.so -> librdkafka++.so.1
-rwxr-xr-x. 1 root root  9723888 8月  10 10:15 librdkafka.so.1
-rwxr-xr-x. 1 root root  1707544 8月  10 10:15 librdkafka++.so.1
-rwxr-xr-x. 1 root root 25033518 8月  10 10:15 librdkafka-static.a
drwxr-xr-x. 2 root root       96 8月  10 10:15 pkgconfig

[root@proftpd-74 proftpd]# ll /usr/local/include/librdkafka/
-rwxr-xr-x. 1 root root 124770 8月  10 10:15 rdkafkacpp.h
-rwxr-xr-x. 1 root root 307127 8月  10 10:15 rdkafka.h
-rwxr-xr-x. 1 root root  11665 8月  10 10:15 rdkafka_mock.h
```



## 2.proftpd installation steps

#### Configure and Install

```bash
tar -zxvf proftpd-1.3.7e.tar.gz -C /usr/src/

mkdir /usr/local/proftpd

cd /usr/src/proftpd-1.3.7e

git clone https://github.com/Castaglia/proftpd-mod_kafka.git contrib/mod_kafka/

./configure  --prefix=/usr/local/proftpd --sysconfdir=/etc/ --enable-nls --enable-openssl --enable-shadow --with-modules=mod_kafka --with-includes=/usr/local/include/librdkafka --with-libraries=/usr/local/lib

make

make install
```

#### Create working directory and user

```bash
mkdir /var/ftp
chmod 777 /var/ftp

mkdri /var/log/ftpd
chmod 755 /var/log/ftpd

useradd proftp -s /sbin/nologin -M
useradd leo -s /sbin/nologin -d /home/leo

ftpasswd  --passwd --file=/usr/local/proftpd/ftpd.passwd --name=leo  --uid=1001  --home=/home/leo  --shell=/sbin/nologin
```



#### proftpd configuration file

vim /etc/proftpd.conf

```bash
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

TraceLog /var/log/ftpd/kafka.log
Trace kafka:20

<Directory "/var/ftp/*" >
<Limit ALL>
AllowAll
</Limit>
</Directory>

<IfModule mod_kafka.c>
  KafkaEngine on
  KafkaLog /var/log/ftpd/kafka.log
  KafkaBroker kafka71:9092,kafka72:9092,kafka73:9092

  LogFormat kafka "%A %a %b %c %D %d %E %{epoch} %F %f %{gid} %g %H %h %I %{iso8601} %J %L %l %m %O %P %p %{protocol} %R %r %{remote-port} %S %s %T %t %U %u %{uid} %V %v %{version}"
  KafkaLogOnEvent ALL kafka topic first-topic
</IfModule>
```



#### Start up:

```bash
/usr/local/proftpd/sbin/proftpd -d10 -c /etc/proftpd.conf
```



