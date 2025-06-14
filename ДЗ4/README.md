### подготовка мастера
```
vi /var/lib/pgsql/17/data/postgresql.conf
full_page_writes = on
wal_level = replica
work_mem = 16MB
shared_buffers = 1GB
effective_cache_size = 4GB
max_wal_senders = 8
max_connections = 1000

create user repl with replication login password 'repl';
create user repl2 with replication login password 'repl2';
```
### подготовка первой реплики
```
mkdir /var/lib/pgsql/17/data2
chmod 0700 /var/lib/pgsql/17/data2
pg_basebackup -U repl -Xs -R -P -D /var/lib/pgsql/17/data2
vi /var/lib/pgsql/17/data2/postgresql.conf
port = 5433
vi /var/lib/pgsql/17/data2/postgresql.auto.conf
primary_conninfo = 'application_name=repl1...
/usr/pgsql-17/bin/pg_ctl start -D /var/lib/pgsql/17/data2/
```
### подготовка второй реплики
```
mkdir /var/lib/pgsql/17/data3
chmod 0700 /var/lib/pgsql/17/data3
pg_basebackup -U repl2 -Xs -R -P -D /var/lib/pgsql/17/data3
vi /var/lib/pgsql/17/data3/postgresql.conf
port = 5434
vi /var/lib/pgsql/17/data3/postgresql.auto.conf
primary_conninfo = 'application_name=repl2...
/usr/pgsql-17/bin/pg_ctl start -D /var/lib/pgsql/17/data3/ 
```
### настройка мастера
```
vi /var/lib/pgsql/17/data/postgresql.conf
synchronous_commit = on
synchronous_standby_names = 'repl1'
select pg_reload_conf();
select application_name, client_addr, sync_state,usename from pg_stat_replication;
 application_name | client_addr | sync_state | usename
------------------+-------------+------------+---------
 repl1            |             | sync       | repl
 repl2            |             | async      | repl2
```
### тест
```
/usr/pgsql-17/bin/pgbench -c 10 -j 4 -T 10 -n -f ~/workload2.sql -U postgres thai
все тесты показали одинаковую скорость работы в режиме синхронная + асинхронная, или синхронная + каскадная асинхронная с синхронной.
скорость повышается, только если синхронную реплику переводим в асинхронный режим.
```
