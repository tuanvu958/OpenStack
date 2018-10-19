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
