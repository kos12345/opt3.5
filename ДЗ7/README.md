### ЗАДАНИЕ
![image](https://github.com/user-attachments/assets/34a0f4a9-9788-41c0-8de6-428fe53ed2c2)

### использованные скрипты
```
CREATE TABLE test (u uuid);
TRUNCATE TABLE test;
INSERT INTO test SELECT gen_random_uuid() FROM generate_series(1,50000);

cat > worload.sql <<EOF
DELETE FROM t WHERE u IN (SELECT u FROM t LIMIT 100);
INSERT INTO t SELECT gen_random_uuid() FROM generate_series(1,100);
EOF

pgbench -c 1 -j 4 -T 600 -f ~/worload.sql -U postgres -n -P 5
```

### опыт выключение автовакуума
```
alter table test SET (autovacuum_enabled = false);

progress: 5.0 s, 47.1 tps, lat 21.117 ms stddev 7.098, 0 failed
progress: 10.0 s, 52.8 tps, lat 18.929 ms stddev 6.948, 0 failed
progress: 15.0 s, 43.8 tps, lat 22.790 ms stddev 7.277, 0 failed
progress: 20.0 s, 42.0 tps, lat 23.789 ms stddev 7.866, 0 failed
progress: 25.0 s, 40.0 tps, lat 24.979 ms stddev 7.541, 0 failed
progress: 30.0 s, 42.2 tps, lat 23.779 ms stddev 9.041, 0 failed
progress: 35.0 s, 40.5 tps, lat 24.707 ms stddev 7.665, 0 failed
progress: 40.0 s, 45.7 tps, lat 21.872 ms stddev 8.060, 0 failed
progress: 45.0 s, 40.7 tps, lat 24.566 ms stddev 8.415, 0 failed
progress: 50.0 s, 41.1 tps, lat 24.238 ms stddev 8.062, 0 failed
progress: 55.0 s, 39.3 tps, lat 25.488 ms stddev 7.591, 0 failed
progress: 60.0 s, 37.4 tps, lat 26.784 ms stddev 9.036, 0 failed
progress: 65.0 s, 36.6 tps, lat 27.250 ms stddev 7.318, 0 failed
progress: 70.0 s, 34.4 tps, lat 29.047 ms stddev 8.713, 0 failed
progress: 75.0 s, 33.2 tps, lat 30.190 ms stddev 10.290, 0 failed
progress: 80.0 s, 35.4 tps, lat 28.320 ms stddev 9.097, 0 failed
progress: 85.0 s, 29.0 tps, lat 34.535 ms stddev 10.168, 0 failed
progress: 90.0 s, 28.2 tps, lat 35.335 ms stddev 10.199, 0 failed
progress: 95.0 s, 31.0 tps, lat 32.343 ms stddev 8.685, 0 failed
progress: 100.0 s, 31.8 tps, lat 31.404 ms stddev 9.223, 0 failed
progress: 105.0 s, 31.2 tps, lat 32.006 ms stddev 8.506, 0 failed
progress: 110.0 s, 31.2 tps, lat 31.858 ms stddev 9.864, 0 failed
progress: 115.0 s, 28.4 tps, lat 35.492 ms stddev 10.641, 0 failed

вывод падение производительнсти 
```
### опыт агрессивный автовакуум
```
ALTER TABLE test SET (
autovacuum_vacuum_threshold = 5,
autovacuum_analyze_threshold = 5,
autovacuum_vacuum_insert_threshold = 5
);
progress: 5.0 s, 46.6 tps, lat 21.343 ms stddev 6.857, 0 failed
progress: 10.0 s, 43.7 tps, lat 22.827 ms stddev 7.667, 0 failed
progress: 15.0 s, 43.4 tps, lat 23.052 ms stddev 7.684, 0 failed
progress: 20.0 s, 44.6 tps, lat 22.373 ms stddev 6.840, 0 failed
progress: 25.0 s, 48.4 tps, lat 20.668 ms stddev 6.464, 0 failed
progress: 30.0 s, 43.0 tps, lat 23.302 ms stddev 7.072, 0 failed
progress: 35.0 s, 50.6 tps, lat 19.764 ms stddev 6.256, 0 failed
progress: 40.0 s, 40.7 tps, lat 24.482 ms stddev 8.444, 0 failed
progress: 45.0 s, 42.1 tps, lat 23.744 ms stddev 6.548, 0 failed
progress: 50.0 s, 42.0 tps, lat 23.824 ms stddev 7.409, 0 failed
progress: 55.0 s, 35.2 tps, lat 28.441 ms stddev 8.050, 0 failed
progress: 60.0 s, 41.8 tps, lat 23.852 ms stddev 9.606, 0 failed
progress: 65.0 s, 37.2 tps, lat 26.984 ms stddev 9.521, 0 failed
progress: 70.0 s, 38.4 tps, lat 25.803 ms stddev 7.450, 0 failed
progress: 75.0 s, 40.8 tps, lat 24.645 ms stddev 9.083, 0 failed
progress: 80.0 s, 46.2 tps, lat 21.639 ms stddev 6.597, 0 failed
progress: 85.0 s, 45.3 tps, lat 22.103 ms stddev 7.774, 0 failed
progress: 90.0 s, 44.9 tps, lat 22.310 ms stddev 6.633, 0 failed
progress: 95.0 s, 42.6 tps, lat 23.396 ms stddev 6.621, 0 failed
progress: 100.0 s, 42.2 tps, lat 23.740 ms stddev 7.783, 0 failed
progress: 105.0 s, 41.5 tps, lat 24.166 ms stddev 8.769, 0 failed
progress: 110.0 s, 42.1 tps, lat 23.724 ms stddev 7.272, 0 failed
progress: 115.0 s, 40.7 tps, lat 24.540 ms stddev 8.266, 0 failed

 пробуем улучшить результат
```

### опыт оптимальный автовакуум
```
ALTER TABLE test SET (
autovacuum_vacuum_threshold = 50,
autovacuum_analyze_threshold = 50,
autovacuum_vacuum_insert_threshold = 50
);
progress: 5.0 s, 49.0 tps, lat 20.140 ms stddev 7.010, 0 failed
progress: 10.0 s, 39.8 tps, lat 25.117 ms stddev 11.163, 0 failed
progress: 15.0 s, 46.5 tps, lat 21.530 ms stddev 7.349, 0 failed
progress: 20.0 s, 42.6 tps, lat 23.472 ms stddev 7.137, 0 failed
progress: 25.0 s, 41.0 tps, lat 24.400 ms stddev 7.725, 0 failed
progress: 30.0 s, 43.4 tps, lat 23.015 ms stddev 9.976, 0 failed
progress: 35.0 s, 50.2 tps, lat 19.893 ms stddev 7.445, 0 failed
progress: 40.0 s, 50.0 tps, lat 19.994 ms stddev 7.669, 0 failed
progress: 45.0 s, 42.5 tps, lat 23.488 ms stddev 7.793, 0 failed
progress: 50.0 s, 45.5 tps, lat 22.126 ms stddev 7.191, 0 failed
progress: 55.0 s, 44.0 tps, lat 22.763 ms stddev 7.557, 0 failed
progress: 60.0 s, 44.5 tps, lat 22.464 ms stddev 5.796, 0 failed
progress: 65.0 s, 55.4 tps, lat 18.066 ms stddev 5.548, 0 failed
progress: 70.0 s, 50.5 tps, lat 19.853 ms stddev 5.683, 0 failed
progress: 75.0 s, 44.8 tps, lat 22.316 ms stddev 6.446, 0 failed
progress: 80.0 s, 45.5 tps, lat 22.026 ms stddev 7.191, 0 failed
progress: 85.0 s, 44.5 tps, lat 22.464 ms stddev 5.796, 0 failed
progress: 90.0 s, 47.2 tps, lat 21.196 ms stddev 9.060, 0 failed
progress: 95.0 s, 55.4 tps, lat 18.066 ms stddev 5.548, 0 failed
progress: 100.0 s, 47.8 tps, lat 20.894 ms stddev 5.525, 0 failed
progress: 105.0 s, 52.3 tps, lat 19.126 ms stddev 5.415, 0 failed
progress: 110.0 s, 50.5 tps, lat 19.853 ms stddev 5.683, 0 failed
progress: 115.0 s, 44.0 tps, lat 22.563 ms stddev 7.245, 0 failed
progress: 120.0 s, 45.5 tps, lat 22.048 ms stddev 7.148, 0 failed
progress: 110.0 s, 50.5 tps, lat 19.853 ms stddev 5.683, 0 failed

вывод. производительность выросла
```
