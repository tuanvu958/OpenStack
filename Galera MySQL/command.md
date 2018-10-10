### Galera Cluster command
1. Bootstrapping a New Cluster
Khởi động node đầu tiên trong cluster với option --wsrep-new-cluster:
```
$ galera_new_cluster
```
2. Thêm node khác vào Cluster
Nếu node đầu tiên có địa chỉ IP là 10.10.1.11, thì để thêm node thứ 2 vào Cluster trên node thứ 2 ta chạy lệnh sau:
```
$ mysqld --wsrep_cluster_address=gcomm://10.10.1.11  # DNS names work as well, IP is preferred for performance
```
> Nếu node bị lỗi mất đồng bộ, không recovery được, cũng có thể dùng lệnh này để join và sync dữ liệu lại
