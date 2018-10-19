### Mục đích
- Muốn add một node mới vào Cluster sử dụng Pacemaker.
- Một node trong cluster bị lỗi, không thể add lại vào Cluster (mysql ko thể khởi động dc).

### Phương pháp
  >Sử dụng mariabackup
1. Cài đặt mariabackup
   ```
   sudo apt-get install mariadb-backup
   ```
2. Backup database trên node đang hoạt động
   ```
   $ mariabackup --backup --target-dir ./backup/ --user backup --password atarasiserver
   $ mariabackup --backup --target-dir ./inc1/ --incremental-basedir ./backup --user backup --password atarasiserver
   ```
   >Tạo 1 user backup trên db để backup
3. Copy thư mục backup của node đang hoạt động sang node mới hoặc node bị failure (dùng scp)
4. Prepare db trên máy mới hoặc máy bị failure
   ```
   $ mariabackup --prepare --target-dir ./backup --user backup --password atarasiserver --apply-log-only
   $ mariabackup --prepare --target-dir ./backup --user backup --password atarasiserver --incremental-dir ./inc1 --apply-log-only
   ```
5. Tạo file grastate.dat
   Chỉnh sửa uuid, seqno phù hợp với nội dung file ./backup/xtrabackup_galera_info
6. Restore db trên máy mới hoặc máy bị failure
   ```
   $ rm -R /var/lib/mysql/*
   $ mariabackup --copy-back --target-dir ./backup/ --user backup --password atarasiserver
   # check /var/lib/mysql/grastate.dat file exist
   $ chown -R mysql:mysql /var/lib/mysql
   ```
7. Done. Start node via pacemaker
