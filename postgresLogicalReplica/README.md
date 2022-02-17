Запускается 2 экземляра postgres11 на 5432 и 5433 портах<br>

testdb1 все нечетные таблици публикуются<br>
testdb3 все четные таблици публикуются<br>
<br><br><br>

Коментарии удалены

master 
<pre>

root@debian:~# sed -e 's/\t//g' -e '/^#/d' -e '/^$/d' /etc/postgresql/11/main/postgresql.conf | sed 's/#/\t\t#/g'
data_directory = '/var/lib/postgresql/11/main'          # use data in another directory
hba_file = '/etc/postgresql/11/main/pg_hba.conf'                # host-based authentication file
ident_file = '/etc/postgresql/11/main/pg_ident.conf'            # ident configuration file
external_pid_file = '/var/run/postgresql/11-main.pid'           # write an extra PID file
port = 5432             # (change requires restart)
max_connections = 100           # (change requires restart)
unix_socket_directories = '/var/run/postgresql'         # comma-separated list of directories
ssl = on
ssl_cert_file = '/etc/ssl/certs/ssl-cert-snakeoil.pem'
ssl_key_file = '/etc/ssl/private/ssl-cert-snakeoil.key'
shared_buffers = 128MB          # min 128kB
dynamic_shared_memory_type = posix              # the default is the first option
wal_level = logical             # minimal, replica, or logical
max_wal_size = 1GB
min_wal_size = 80MB
log_line_prefix = '%m [%p] %q%u@%d '            # special values:
log_timezone = 'UTC'
cluster_name = '11/main'                # added to process titles if nonempty
stats_temp_directory = '/var/run/postgresql/11-main.pg_stat_tmp'
datestyle = 'iso, mdy'
timezone = 'UTC'
lc_messages = 'en_US.UTF-8'             # locale for system error message
lc_monetary = 'en_US.UTF-8'             # locale for monetary formatting
lc_numeric = 'en_US.UTF-8'              # locale for number formatting
lc_time = 'en_US.UTF-8'         # locale for time formatting
default_text_search_config = 'pg_catalog.english'
include_dir = 'conf.d'          # include files ending in '.conf' from

sed -e 's/\t//g' -e '/^#/d' -e '/^$/d' /etc/postgresql/11/main/pg_hba.conf  | sed 's/#/\t\t#/g'
host    replication    postgres    0.0.0.0/0    md5
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5
</pre>

slave
<pre>
root@debian:~# sed -e 's/\t//g' -e '/^#/d' -e '/^$/d' /etc/postgresql/11/slave/postgresql.conf | sed 's/#/\t\t#/g'
data_directory = '/var/lib/postgresql/11/slave'         # use data in another directory
hba_file = '/etc/postgresql/11/slave/pg_hba.conf'               # host-based authentication file
ident_file = '/etc/postgresql/11/slave/pg_ident.conf'           # ident configuration file
external_pid_file = '/var/run/postgresql/11-slave.pid'          # write an extra PID file
port = 5433             # (change requires restart)
max_connections = 100           # (change requires restart)
unix_socket_directories = '/var/run/postgresql'         # comma-separated list of directories
ssl = on
ssl_cert_file = '/etc/ssl/certs/ssl-cert-snakeoil.pem'
ssl_key_file = '/etc/ssl/private/ssl-cert-snakeoil.key'
shared_buffers = 128MB          # min 128kB
dynamic_shared_memory_type = posix              # the default is the first option
max_wal_size = 1GB
min_wal_size = 80MB
log_line_prefix = '%m [%p] %q%u@%d '            # special values:
log_timezone = 'UTC'
cluster_name = '11/slave'               # added to process titles if nonempty
stats_temp_directory = '/var/run/postgresql/11-slave.pg_stat_tmp'
datestyle = 'iso, mdy'
timezone = 'UTC'
lc_messages = 'en_US.UTF-8'             # locale for system error message
lc_monetary = 'en_US.UTF-8'             # locale for monetary formatting
lc_numeric = 'en_US.UTF-8'              # locale for number formatting
lc_time = 'en_US.UTF-8'         # locale for time formatting
default_text_search_config = 'pg_catalog.english'
include_dir = 'conf.d'          # include files ending in '.conf' from



root@debian:~# sed -e 's/\t//g' -e '/^#/d' -e '/^$/d' /etc/postgresql/11/slave/pg_hba.conf  | sed 's/#/\t\t#/g'
local   all             postgres                                peer
local   all             all                                     peer
host    all             all             127.0.0.1/32            md5
host    all             all             ::1/128                 md5
local   replication     all                                     peer
host    replication     all             127.0.0.1/32            md5
host    replication     all             ::1/128                 md5

</pre>
