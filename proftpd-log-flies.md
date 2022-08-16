### All log files

```bash
[root@proftpd-74 ftpd]# ll /var/log/ftpd/
总用量 28
-rw-r-----. 1 root root     0 8月  15 17:25 kafka.log
-rw-r-----. 1 root root 21154 8月  15 17:28 proftpd.log
-rw-r--r--. 1 root root    98 8月  15 17:25 transfer.log
```



### Proftpd log

```bash
2022-08-15 17:25:56,046 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching PRE_CMD command 'PORT 192,168,118,1,197,252' to mod_core
2022-08-15 17:25:56,046 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching PRE_CMD command 'PORT 192,168,118,1,197,252' to mod_core
2022-08-15 17:25:56,046 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching CMD command 'PORT 192,168,118,1,197,252' to mod_core
2022-08-15 17:25:56,046 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): in dir_check_full(): path = '/', fullpath = '/var/ftp/'
2022-08-15 17:25:56,046 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching LOG_CMD command 'PORT 192,168,118,1,197,252' to mod_kafka
2022-08-15 17:25:56,046 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching LOG_CMD command 'PORT 192,168,118,1,197,252' to mod_log
2022-08-15 17:25:56,047 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching PRE_CMD command 'STOR /zookeeper.tar.gz' to mod_core
2022-08-15 17:25:56,047 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching PRE_CMD command 'STOR /zookeeper.tar.gz' to mod_core
2022-08-15 17:25:56,047 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching PRE_CMD command 'STOR /zookeeper.tar.gz' to mod_xfer
2022-08-15 17:25:56,047 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): in dir_check_full(): path = '/zookeeper.tar.gz', fullpath = '/var/ftp/zookeeper.tar.gz'
2022-08-15 17:25:56,047 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): in dir_check_full(): setting umask to 0022 (was 0022)
2022-08-15 17:25:56,047 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching CMD command 'STOR /zookeeper.tar.gz' to mod_xfer
2022-08-15 17:25:56,048 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): active data connection opened - local  : 192.168.118.74:35841
2022-08-15 17:25:56,048 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): active data connection opened - remote : 192.168.118.1:50684
2022-08-15 17:25:56,562 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching POST_CMD command 'STOR /zookeeper.tar.gz' to mod_xfer
2022-08-15 17:25:56,562 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching LOG_CMD command 'STOR /zookeeper.tar.gz' to mod_kafka
2022-08-15 17:25:56,562 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching LOG_CMD command 'STOR /zookeeper.tar.gz' to mod_log
2022-08-15 17:25:56,562 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): dispatching LOG_CMD command 'STOR /zookeeper.tar.gz' to mod_xfer
2022-08-15 17:25:56,562 proftpd-74 proftpd[3652] proftpd-74 (192.168.118.1[192.168.118.1]): Transfer completed: 12649765 bytes in 0.51 seconds

```



### Transfer log

```bash
[root@proftpd-74 ftpd]# cat transfer.log 
Mon Aug 15 17:25:56 2022 0 192.168.118.1 12649765 /var/ftp/zookeeper.tar.gz a _ i r leo ftp 0 * c
```