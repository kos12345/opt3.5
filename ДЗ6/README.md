### ЗАДАНИЕ
![image](https://github.com/user-attachments/assets/ff2ada9f-c60c-46b0-99b7-16272773fcc2)
### используемые инструменты
```
скрипт генерации нагрузки
psql -U postgres -c  "
create table test as
select generate_series as id
	, generate_series::text || (random() * 10)::text as col2
    , (array['Yes', 'No', 'Maybe'])[floor(random() * 3 + 1)] as is_okay
from generate_series(1, 500000);update test  set  is_okay  = 'NeverNever' where id%2  = 0 ;drop table test;"

скрипт получения информации об объеме wal-файлов сгенерированных скриптом
psql -U postgres -c  "select pg_current_wal_lsn();"
0/4C2ED4B8
psql -U postgres -c  "select pg_size_pretty(pg_wal_lsn_diff(pg_current_wal_lsn(),'0/4C2ED4B8'));"

изменяемые параметры
wal_level  minimal -> replica - > logical
max_wal_senders 0 -> 2
full_page_writes off -> on
wal_compression  off -> on
min_wal_size  100MB -> 1GB
checkpoint  1min -> 10 min
```
### результаты
| wal_level | minimal | replica | logical |
| --- | --- |--- |--- |
| wal size | 12 kB | 85 MB | 85 MB |


