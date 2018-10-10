### Galera Cluster command

1. Bootstrapping a New Cluster <br>
Khởi động node đầu tiên trong cluster với option --wsrep-new-cluster:
```
$ galera_new_cluster
```
2. Thêm node khác vào Cluster <br>
Nếu node đầu tiên có địa chỉ IP là 10.10.1.11, thì để thêm node thứ 2 vào Cluster trên node thứ 2 ta chạy lệnh sau:
```
$ mysqld --wsrep_cluster_address=gcomm://10.10.1.11  # DNS names work as well, IP is preferred for performance
```
> Nếu node bị lỗi mất đồng bộ, không recovery được, cũng có thể dùng lệnh này để join và sync dữ liệu lại
### Configuration and Monitoring
1. Parameter bắt buộc phải config cho Galera làm việc:
  - wsrep_provider — Path to the Galera library
  - wsrep_cluster_address - xem [cluster connection URL](https://mariadb.com/kb/en/galera-cluster-address/)
  - binlog_format=ROW 
  - default_storage_engine=InnoDB
  - innodb_autoinc_lock_mode=2
  - innodb_doublewrite=1 the default) when using Galera provider of version >= 2.0.
  - query_cache_size=0 (only for versions prior to 5.5.40-galera, 10.0.14-galera and 10.1.2)
  - wsrep_on=ON — Enable wsrep replication (starting 10.1.1)
2. Tùy chọn
These are just optimizations made relatively safe by synchronous replication — you always recover from another node.
  - innodb_flush_log_at_trx_commit=0 - [xem thêm](https://mariadb.com/kb/en/xtradbinnodb-server-system-variables/#innodb_flush_log_at_trx_commit)
3. Moniroring
Xem các biến trạng thái của Galera Cluster:
```
SHOW STATUS LIKE 'wsrep_%';
```
### Link tham khảo
[Getting started with mariadb galera cluseter](https://mariadb.com/kb/en/library/getting-started-with-mariadb-galera-cluster/)
