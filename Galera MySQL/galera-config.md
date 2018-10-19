### Host OS: Ubuntu 16.04.5 LTS

### Cấu hình galera tham khảo
```
[galera]
binlog_format=ROW
max_connections=8192
query_cache_size=0
query_cache_type=0
max_allowed_packet=256M
innodb_log_buffer_size = 32M
innodb_log_file_size = 2047M
default-storage-engine=innodb
innodb_autoinc_lock_mode=2
bind-address=0.0.0.0
# Galera Provider Configuration
wsrep_on=ON
wsrep_provider=/usr/lib/galera/libgalera_smm.so
# Galera Cluster Configuration
wsrep_cluster_name="galera_cluster"
wsrep_cluster_address="gcomm://10.10.1.11,10.10.1.12,10.10.1.13"

wsrep_sst_method=mariabackup
wsrep_slave_threads=4
innodb_locks_unsafe_for_binlog=1
wsrep_sst_auth=galera:tHiY4w
wsrep_provider_options="socket.ssl_cert=/etc/mysql/ssl/certfile.pem;socket.ssl_key=/etc/mysql/ssl/keyfile.pem;gcache.size=5G"
wsrep_replicate_myisam=ON

# Galera Node Configuration
wsrep_node_address="10.10.1.11"
wsrep_node_name="controller01"

[sst]
compressor="gzip"
decompressor="gzip -dc"
rebuild=ON
compact=ON
encrypt=3
tkey=/etc/mysql/ssl/keyfile.pem
tcert=/etc/mysql/ssl/certfile.pem
```

### Cấu hình pacemaker
1. Tắt tự khởi động dịch vụ mysql
```
$ systemctl disable mariadb.service 
$ systemctl disable mysql
```
2. Cấu hình pacemaker
```
primitive db galera \
        params enable_creation=true wsrep_cluster_address="gcomm://controller01,controller02,controller03" check_user=checkst check_passwd=atarasiserver pid="/var/run/mysqld/mysqld.pid" socket="/var/run/mysqld/mysqld.sock" \
        op start interval=0s timeout=120 \
        op stop interval=0s timeout=120 \
        op monitor interval=20 timeout=30 \
        op monitor interval=10 role=Master timeout=30 \
        op monitor interval=30 role=Slave timeout=30 \
        op promote interval=0s timeout=300 \
        op demote interval=0s timeout=120
ms db-master db \
        meta master-max=3
```
Khởi động resource VirtualIP cùng node với DB Master (tránh trờng hợp vIP ở trên Node ở trạng thái Slave)
```
$ pcs constraint colocation add vips with master db-master
```
> checkst là user của mariadb dùng để check

3. Quản lý cluster qua GUI
   - Cài đặt pcsd
     ```
     $ apt install pcsd
     $ service pcsd restart
     ```
   - Để đăng nhập, set password cho user hacluster
     ```
     $ passwd hacluster
     ```
   - Đăng nhập GUI bằng địa chỉ
     > https://your-ip:2224
   


