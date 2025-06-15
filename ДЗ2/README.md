### ЗАДАНИЕ
![image](https://github.com/user-attachments/assets/d2de6a7a-fd54-4bd1-8795-5da73d3e0351)

### подключение без pgbouncer  
/usr/pgsql-17/bin/pgbench -c 600 -j 8 -T 10 -n -f ~/workload2.sql -U postgres thai  
pgbench (17.5)  
transaction type: /var/lib/pgsql/workload2.sql  
scaling factor: 1  
query mode: simple  
number of clients: 600  
number of threads: 8  
maximum number of tries: 1  
duration: 10 s  
number of transactions actually processed: 223  
number of failed transactions: 0 (0.000%)  
latency average = 11631.904 ms  
initial connection time = 8110.296 ms  
**tps = 51.582269** (without initial connection time)  

### подключение через pgbouncer -p 6432 -h 127.0.0.1  
/usr/pgsql-17/bin/pgbench -c 600 -j 8 -T 10 -n -f ~/workload2.sql -U postgres thai -p 6432 -h 127.0.0.1  
Password:  
pgbench (17.5)  
transaction type: /var/lib/pgsql/workload2.sql  
scaling factor: 1  
query mode: simple  
number of clients: 600  
number of threads: 8  
maximum number of tries: 1  
duration: 10 s  
number of transactions actually processed: 2377  
number of failed transactions: 0 (0.000%)  
latency average = 1481.157 ms  
initial connection time = 5589.074 ms  
**tps = 405.088677** (without initial connection time)  

### вывод. пулер необходим.  
