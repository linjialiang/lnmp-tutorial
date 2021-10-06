# vsftpd 配置文件

路径： /etc/vsftpd.conf

```conf
anonymous_enable=NO
local_enable=YES
ssl_enable=NO
download_enable=YES
write_enable=YES

file_open_mode=0666
local_umask=026

listen=NO
listen_ipv6=YES
listen_port=21

port_enable=NO
pasv_enable=YES
pasv_min_port=21001
pasv_max_port=21010
accept_timeout=60

idle_session_timeout=600
data_connection_timeout=500
local_max_rate=8192
max_per_ip=3
max_clients=10
max_login_fails=3
trans_chunk_size=8192

pam_service_name=vsftpd-guest

virtual_use_local_privs=yes
guest_enable=yes
guest_username=www
local_root=/server/default
user_config_dir=/server/vsftpd

chroot_local_user=YES
chroot_list_enable=YES
chroot_list_file=/etc/vsftpd/chroot_list
userlist_enable=YES
userlist_deny=YES
userlist_file=/etc/vsftpd/user_list
```
