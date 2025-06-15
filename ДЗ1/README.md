### ЗАДАНИЕ

![image](https://github.com/user-attachments/assets/55a6a967-0f0d-4dd0-8997-0b535c97016c)


### подготовка  
dnf install postgresql17 postgresql17-contrib postgresql17-server  
  
/usr/pgsql-17/bin/initdb -D /var/lib/pgsql/17/data --data-checksums  
  
sudo systemctl start postgresql-17  
  
cd ~ && wget https://storage.googleapis.com/thaibus/thai_small.tar.gz && tar -xf thai_small.tar.gz && psql < thai.sql  
  
cat > ~/workload2.sql << EOL  
  
INSERT INTO book.tickets (fkRide, fio, contact, fkSeat)  
VALUES (  
	ceil(random()*100)  
	, (array(SELECT fam FROM book.fam))[ceil(random()*110)]::text || ' ' ||  
    (array(SELECT nam FROM book.nam))[ceil(random()*110)]::text  
    ,('{"phone":"+7' || (1000000000::bigint + floor(random()*9000000000)::bigint)::text || '"}')::jsonb  
    , ceil(random()*100));  
  
EOL  


### тест первый с настройками по умолчанию  
/usr/pgsql-17/bin/pgbench -c 10 -j 4 -T 10 -n -f ~/workload2.sql -U postgres thai  
  
pgbench (17.5)  
transaction type: /var/lib/pgsql/workload2.sql  
scaling factor: 1  
query mode: simple  
number of clients: 10  
number of threads: 4  
maximum number of tries: 1  
duration: 10 s  
number of transactions actually processed: 4386  
number of failed transactions: 0 (0.000%)  
latency average = 22.787 ms  
initial connection time = 58.823 ms  
**tps = 438.838553** (without initial connection time)  
  
### тест второй с настройками postgresql  
vi /var/lib/pgsql/17/data/postgresql.conf  
random_page_cost = '1.1'  
fsync = off  
full_page_writes = off  
synchronous_commit = off  
effective_io_concurrency=200  
wal_level = minimal  
work_mem = 32MB  
shared_buffers = 4GB  
effective_cache_size = 4GB  
max_wal_senders = 0  
  
sudo systemctl restart postgresql-17  
  
pgbench (17.5)  
transaction type: /var/lib/pgsql/workload2.sql  
scaling factor: 1  
query mode: simple  
number of clients: 10  
number of threads: 4  
maximum number of tries: 1  
duration: 10 s  
number of transactions actually processed: 8674  
number of failed transactions: 0 (0.000%)  
latency average = 11.625 ms  
initial connection time = 47.047 ms  
**tps = 860.205073** (without initial connection time)  
  
### тест 3. с отключением логгирования таблиц  
alter table book.tickets set UNLOGGED;  
alter table book.seat set UNLOGGED;  
alter table book.ride set UNLOGGED;  
alter table book.bus set UNLOGGED;  
alter table book.seatcategory set UNLOGGED;  
alter table book.schedule set UNLOGGED;  
alter table book.nam set UNLOGGED;  
alter table book.fam set UNLOGGED;  
alter table book.busroute set UNLOGGED;  
alter table book.busstation set UNLOGGED;  
  
pgbench (17.5)  
transaction type: /var/lib/pgsql/workload2.sql  
scaling factor: 1  
query mode: simple  
number of clients: 10  
number of threads: 4  
maximum number of tries: 1  
duration: 10 s  
number of transactions actually processed: 9892  
number of failed transactions: 0 (0.000%)  
latency average = 10.133 ms  
initial connection time = 46.550 ms  
**tps = 986.894023** (without initial connection time)  
  
### вывод. добились более чем двухкратного ускорения работы.  
