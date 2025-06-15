### подготовка сервера
```
dnf install timescaledb-17

vi /var/lib/pgsql/17/data/postgresql.conf
enable_partitionwise_join = on
enable_partitionwise_aggregate = on
enable_partition_pruning = on
shared_preload_libraries = 'timescaledb'

systemctl restart postgresql-17
```
### подготовка таблицы. используем timescaledb
```
psql -d thai

create extension timescaledb;
CREATE TABLE book.ride1 (LIKE book.ride INCLUDING DEFAULTS INCLUDING CONSTRAINTS EXCLUDING INDEXES);
SELECT create_hypertable('book.ride1', 'startdate', chunk_time_interval => INTERVAL '1 day');
INSERT INTO book.ride1 SELECT * FROM book.ride;
ALTER TABLE book.ride1 ADD CONSTRAINT ride_fkbus_fkey1 FOREIGN KEY (fkbus) REFERENCES book.bus(id);
ALTER TABLE book.ride1 ADD CONSTRAINT ride_fkschedule_fkey1 FOREIGN KEY (fkschedule) REFERENCES book.schedule(id);
alter table book.ride rename to rideold;
alter table book.ride1 rename to ride;
```
### тест со всеми партициям
```
cat > ~/workload_allparts.sql << EOL
\set r random(1, 5216503)
SELECT t.id, r.startdate, t.fio, t.contact, bf.city, bf.name, bt.city, bt.name, b.model 
FROM book.tickets t
JOIN book.ride r ON t.fkride = r.id
JOIN book.schedule s ON r.fkschedule = s.id
JOIN book.busroute br  ON s.fkroute = br.id
JOIN book.busstation bf ON br.fkbusstationfrom = bf.id
JOIN book.busstation bt ON br.fkbusstationto = bt.id
JOIN book.bus b ON r.fkbus = b.id
WHERE t.id = :r;
EOL
/usr/pgsql-17/bin/pgbench -c 10 -j 4 -T 10 -n -f ~/workload_allparts.sql -U postgres thai
tps = 101.925728 (without initial connection time)
```
### тест с 10 партициями 
```
cat > ~/workload_10parts.sql << EOL
\set r random(1, 5216503)
SELECT t.id, r.startdate, t.fio, t.contact, bf.city, bf.name, bt.city, bt.name, b.model 
FROM book.tickets t
JOIN book.ride r ON t.fkride = r.id
JOIN book.schedule s ON r.fkschedule = s.id
JOIN book.busroute br  ON s.fkroute = br.id
JOIN book.busstation bf ON br.fkbusstationfrom = bf.id
JOIN book.busstation bt ON br.fkbusstationto = bt.id
JOIN book.bus b ON r.fkbus = b.id
WHERE t.id = :r and r.startdate BETWEEN ('2000-01-03') and ('2000-01-12');
EOL
/usr/pgsql-17/bin/pgbench -c 10 -j 4 -T 10 -n -f ~/workload_10parts.sql -U postgres thai
tps = 385.114751 (without initial connection time)
```
### тест с 1 партицией
```
cat > ~/workload_1parts.sql << EOL
\set r random(1, 5216503)
SELECT t.id, r.startdate, t.fio, t.contact, bf.city, bf.name, bt.city, bt.name, b.model 
FROM book.tickets t
JOIN book.ride r ON t.fkride = r.id
JOIN book.schedule s ON r.fkschedule = s.id
JOIN book.busroute br  ON s.fkroute = br.id
JOIN book.busstation bf ON br.fkbusstationfrom = bf.id
JOIN book.busstation bt ON br.fkbusstationto = bt.id
JOIN book.bus b ON r.fkbus = b.id
WHERE t.id = :r and r.startdate BETWEEN ('2000-01-03') and ('2000-01-03');
EOL
/usr/pgsql-17/bin/pgbench -c 10 -j 4 -T 10 -n -f ~/workload_1parts.sql -U postgres thai
tps = 1556.190613 (without initial connection time)
```

### вывод. чем сильнее мы ограничиваем секции, тем выше производительность.




