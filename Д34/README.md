### подготовка
```
vi /var/lib/pgsql/17/data/postgresql.conf
full_page_writes = on
wal_level = replica
work_mem = 16MB
shared_buffers = 1GB
effective_cache_size = 4GB
max_wal_senders = 8
max_connections = 1000

```
