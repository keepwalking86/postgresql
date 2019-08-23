## Installation on Ubuntu-16.04

- Add repo and install postgresql-11

```
echo "deb http://apt.postgresql.org/pub/repos/apt/ xenial-pgdg main" > \
  /etc/apt/sources.list.d/pgdg.list
wget --quiet -O - https://www.postgresql.org/media/keys/ACCC4CF8.asc | sudo apt-key add -
apt-get update
apt-get install -y postgresql-11 postgresql-client-11 postgresql-contrib-11
```

- Access postgresql

```
sudo su - postgres
psql
```

## Install on CentOS 7

- Download and install

```
yum install https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
yum install postgresql11 postgresql11-server
```

- initialize the database

`/usr/pgsql-11/bin/postgresql-11-setup initdb`

- enable automatic start

```
systemctl enable postgresql-11
systemctl start postgresql-11
```

- Show version

`psql -V`
psql (PostgreSQL) 11.5

## Access Postgres

Postgres thiết lập với kiểu chứng thực **ident**, nghĩa là nó kết hợp giữa Postgres roles với tài khoản hệ thống của Unix/Linux.

Tài khoản postgres được tạo và thiết lập với role là Postgres.

- switch account and access postgresql

```
sudo -i -u postgres
psql
```

- access postgresql

`sudo -u postgres psql`


## Allow remote connections

Mặc định trong cấu hình PostgreSQL chỉ cho phép truy cập localhost và truy cập với unix socket và không cần truy cập password.

Vì vậy, để cho phép truy cập từ xa, chúng ta thiết lập tùy chọn cấu hình như sau:

- Thay đổi listen address

Sửa nội dung tệp tệp /var/lib/pgsql/11/data/postgresql.conf và chỉ định địa chỉ IP của PostgreSQL sẽ được gán

`listen_addresses = '*' #allow all network interface`

- Cho phép truy cập từ máy từ xa

PostgreSQL sử dụng tệp tin cấu hình để kiểm soát truy cập và chứng thực từ xa (Client authentication), mà quy ước trong tệp tin pg_hba.conf

Định dạng của tệp tin pg_hba.conf là một tập các bản ghi, với mỗi bản ghi là một dòng.

Bản ghi trong tệp cấu hình pg_hba.conf có các tùy chọn định dạng sau:

```
local      database  user  auth-method  [auth-options]
host       database  user  address  auth-method  [auth-options]
hostssl    database  user  address  auth-method  [auth-options]
hostnossl  database  user  address  auth-method  [auth-options]
host       database  user  IP-address  IP-mask  auth-method  [auth-options]
hostssl    database  user  IP-address  IP-mask  auth-method  [auth-options]
hostnossl  database  user  IP-address  IP-mask  auth-method  [auth-options]
```

Các thiết lập mặc định như sau

```
# TYPE  DATABASE        USER            ADDRESS                 METHOD
# "local" is for Unix domain socket connections only
local   all             all                                     peer
# IPv4 local connections:
host    all             all             127.0.0.1/32            ident
# IPv6 local connections:
host    all             all             ::1/128                 ident
# Allow replication connections from localhost, by a user with the
# replication privilege.
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            ident
host    replication     all             ::1/128                 ident
```

Ví dụ: Cho phép truy cập từ IP 192.168.1.110 đến postgresql qua phương thức chứng thực md5.
Khi đó, chúng ta thêm nội dung sau vào tệp cấu hình /var/lib/pgsql/11/data/pg_hba.conf

`host    all             all             192.168.1.110/32            md5`

Cuối cùng thực hiện restart postgresql

`systemctl restart postgresql-11.service`

Read more về các phương thức chứng thực tại: [https://www.postgresql.org/docs/11/auth-pg-hba-conf.html](https://www.postgresql.org/docs/11/auth-pg-hba-conf.html)


