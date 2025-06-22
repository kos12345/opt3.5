### ЗАДАНИЕ
![image](https://github.com/user-attachments/assets/6b0b096a-8731-4f86-88d7-e2778160016b)
### РАБОТА

### сканирование таблицы
```
Sort  (cost=1701167.95..1701167.97 rows=8 width=23) (actual time=26165.303..26168.956 rows=11 loops=1)
  Output: payment_type, (round(((sum(tips) / sum((tips + fare))) * '100'::double precision))), (count(*))
  Sort Key: (count(*)) DESC
  Sort Method: quicksort  Memory: 25kB
  Buffers: shared hit=304 read=1448967
  ->  Finalize GroupAggregate  (cost=1701165.66..1701167.83 rows=8 width=23) (actual time=26165.247..26168.939 rows=11 loops=1)
        Output: payment_type, round(((sum(tips) / sum((tips + fare))) * '100'::double precision)), count(*)
        Group Key: taxi_trips.payment_type
        Buffers: shared hit=304 read=1448967
        ->  Gather Merge  (cost=1701165.66..1701167.53 rows=16 width=31) (actual time=26165.221..26168.898 rows=31 loops=1)
              Output: payment_type, (PARTIAL sum(tips)), (PARTIAL sum((tips + fare))), (PARTIAL count(*))
              Workers Planned: 2
              Workers Launched: 2
              Buffers: shared hit=304 read=1448967
              ->  Sort  (cost=1700165.64..1700165.66 rows=8 width=31) (actual time=26149.921..26149.924 rows=10 loops=3)
                    Output: payment_type, (PARTIAL sum(tips)), (PARTIAL sum((tips + fare))), (PARTIAL count(*))
                    Sort Key: taxi_trips.payment_type
                    Sort Method: quicksort  Memory: 25kB
                    Buffers: shared hit=304 read=1448967
                    Worker 0:  actual time=26140.670..26140.672 rows=11 loops=1
                      Sort Method: quicksort  Memory: 25kB
                      Buffers: shared hit=94 read=458794
                    Worker 1:  actual time=26148.530..26148.534 rows=10 loops=1
                      Sort Method: quicksort  Memory: 25kB
                      Buffers: shared hit=88 read=508699
                    ->  Partial HashAggregate  (cost=1700165.44..1700165.52 rows=8 width=31) (actual time=26148.492..26148.496 rows=10 loops=3)
                          Output: payment_type, PARTIAL sum(tips), PARTIAL sum((tips + fare)), PARTIAL count(*)
                          Group Key: taxi_trips.payment_type
                          Batches: 1  Memory Usage: 24kB
                          Buffers: shared hit=288 read=1448967
                          Worker 0:  actual time=26140.618..26140.622 rows=11 loops=1
                            Batches: 1  Memory Usage: 24kB
                            Buffers: shared hit=86 read=458794
                          Worker 1:  actual time=26144.314..26144.319 rows=10 loops=1
                            Batches: 1  Memory Usage: 24kB
                            Buffers: shared hit=80 read=508699
                          ->  Parallel Seq Scan on public.taxi_trips  (cost=0.00..1560770.75 rows=11151575 width=23) (actual time=4.959..17609.706 rows=8917894 loops=3)
                                Output: unique_key, taxi_id, trip_start_timestamp, trip_end_timestamp, trip_seconds, trip_miles, pickup_census_tract, dropoff_census_tract, pickup_community_area, dropoff_community_area, fare, tips, tolls, extras, trip_total, payment_type, company, pickup_latitude, pickup_longitude, pickup_location, dropoff_latitude, dropoff_longitude, dropoff_location
                                Buffers: shared hit=288 read=1448967
                                Worker 0:  actual time=5.801..17245.346 rows=8483979 loops=1
                                  Buffers: shared hit=86 read=458794
                                Worker 1:  actual time=9.011..18502.520 rows=9411255 loops=1
                                  Buffers: shared hit=80 read=508699
Query Identifier: -3920066491726817770
Planning Time: 0.087 ms
Execution Time: 26169.009 ms
```
### индекс  (payment_type,tips,fare)
```
Sort  (cost=958386.67..958386.69 rows=8 width=23) (actual time=10015.597..10033.727 rows=11 loops=1)
  Output: payment_type, (round(((sum(tips) / sum((tips + fare))) * '100'::double precision))), (count(*))
  Sort Key: (count(*)) DESC
  Sort Method: quicksort  Memory: 25kB
  Buffers: shared hit=712949 read=141951
  ->  Finalize GroupAggregate  (cost=1000.59..958386.55 rows=8 width=23) (actual time=10015.580..10033.702 rows=11 loops=1)
        Output: payment_type, round(((sum(tips) / sum((tips + fare))) * '100'::double precision)), count(*)
        Group Key: taxi_trips.payment_type
        Buffers: shared hit=712949 read=141951
        ->  Gather Merge  (cost=1000.59..958386.25 rows=16 width=31) (actual time=10015.412..10033.612 rows=22 loops=1)
              Output: payment_type, (PARTIAL sum(tips)), (PARTIAL sum((tips + fare))), (PARTIAL count(*))
              Workers Planned: 2
              Workers Launched: 2
              Buffers: shared hit=712949 read=141951
              ->  Partial GroupAggregate  (cost=0.56..957384.38 rows=8 width=31) (actual time=6001.674..8668.761 rows=7 loops=3)
                    Output: payment_type, PARTIAL sum(tips), PARTIAL sum((tips + fare)), PARTIAL count(*)
                    Group Key: taxi_trips.payment_type
                    Buffers: shared hit=712949 read=141951
                    Worker 0:  actual time=6001.242..10001.556 rows=10 loops=1
                      Buffers: shared hit=330268 read=56363
                    Worker 1:  actual time=5990.196..9991.062 rows=10 loops=1
                      Buffers: shared hit=355801 read=56887
                    ->  Parallel Index Only Scan using i_test on public.taxi_trips  (cost=0.56..817949.61 rows=11154775 width=23) (actual time=0.060..6064.196 rows=8917894 loops=3)
                          Output: payment_type, tips, fare
                          Heap Fetches: 0
                          Buffers: shared hit=712949 read=141951
                          Worker 0:  actual time=0.048..6999.706 rows=10457056 loops=1
                            Buffers: shared hit=330268 read=56363
                          Worker 1:  actual time=0.092..6912.431 rows=10470949 loops=1
                            Buffers: shared hit=355801 read=56887
Query Identifier: -3920066491726817770
Planning Time: 0.142 ms
Execution Time: 10033.776 ms
```
### индекс  (payment_type) include (tips,fare)
```
include
Sort  (cost=955845.88..955845.90 rows=8 width=23) (actual time=9694.185..9721.140 rows=11 loops=1)
  Output: payment_type, (round(((sum(tips) / sum((tips + fare))) * '100'::double precision))), (count(*))
  Sort Key: (count(*)) DESC
  Sort Method: quicksort  Memory: 25kB
  Buffers: shared hit=488 read=141952
  ->  Finalize GroupAggregate  (cost=1000.59..955845.76 rows=8 width=23) (actual time=9694.144..9721.121 rows=11 loops=1)
        Output: payment_type, round(((sum(tips) / sum((tips + fare))) * '100'::double precision)), count(*)
        Group Key: taxi_trips.payment_type
        Buffers: shared hit=488 read=141952
        ->  Gather Merge  (cost=1000.59..955845.46 rows=16 width=31) (actual time=9694.037..9721.089 rows=22 loops=1)
              Output: payment_type, (PARTIAL sum(tips)), (PARTIAL sum((tips + fare))), (PARTIAL count(*))
              Workers Planned: 2
              Workers Launched: 2
              Buffers: shared hit=488 read=141952
              ->  Partial GroupAggregate  (cost=0.56..954843.59 rows=8 width=31) (actual time=5815.346..8391.595 rows=7 loops=3)
                    Output: payment_type, PARTIAL sum(tips), PARTIAL sum((tips + fare)), PARTIAL count(*)
                    Group Key: taxi_trips.payment_type
                    Buffers: shared hit=488 read=141952
                    Worker 0:  actual time=5803.144..9667.406 rows=10 loops=1
                      Buffers: shared hit=221 read=55991
                    Worker 1:  actual time=5813.992..9678.394 rows=10 loops=1
                      Buffers: shared hit=221 read=57519
                    ->  Parallel Index Only Scan using i_taxi_trips2 on public.taxi_trips  (cost=0.56..815748.24 rows=11127622 width=23) (actual time=1.485..5703.286 rows=8917894 loops=3)
                          Output: payment_type, tips, fare
                          Heap Fetches: 0
                          Buffers: shared hit=488 read=141952
                          Worker 0:  actual time=0.063..6527.514 rows=10340534 loops=1
                            Buffers: shared hit=221 read=55991
                          Worker 1:  actual time=4.342..6547.575 rows=10640065 loops=1
                            Buffers: shared hit=221 read=57519
Query Identifier: -3920066491726817770
Planning Time: 0.097 ms
Execution Time: 9721.189 ms
```
### секционирование partition by list (payment_type) и   индекс  (payment_type,tips,fare)
```
Sort  (cost=1012428.17..1012428.19 rows=8 width=23) (actual time=9731.018..9758.349 rows=11 loops=1)
  Output: taxi_trips_part.payment_type, (round(((sum(taxi_trips_part.tips) / sum((taxi_trips_part.tips + taxi_trips_part.fare))) * '100'::double precision))), (count(*))
  Sort Key: (count(*)) DESC
  Sort Method: quicksort  Memory: 25kB
  Buffers: shared hit=128 read=141969
  ->  Finalize GroupAggregate  (cost=1012425.88..1012428.05 rows=8 width=23) (actual time=9730.986..9758.334 rows=11 loops=1)
        Output: taxi_trips_part.payment_type, round(((sum(taxi_trips_part.tips) / sum((taxi_trips_part.tips + taxi_trips_part.fare))) * '100'::double precision)), count(*)
        Group Key: taxi_trips_part.payment_type
        Buffers: shared hit=128 read=141969
        ->  Gather Merge  (cost=1012425.88..1012427.75 rows=16 width=31) (actual time=9730.975..9758.310 rows=14 loops=1)
              Output: taxi_trips_part.payment_type, (PARTIAL sum(taxi_trips_part.tips)), (PARTIAL sum((taxi_trips_part.tips + taxi_trips_part.fare))), (PARTIAL count(*))
              Workers Planned: 2
              Workers Launched: 2
              Buffers: shared hit=128 read=141969
              ->  Sort  (cost=1011425.85..1011425.87 rows=8 width=31) (actual time=9710.055..9710.061 rows=5 loops=3)
                    Output: taxi_trips_part.payment_type, (PARTIAL sum(taxi_trips_part.tips)), (PARTIAL sum((taxi_trips_part.tips + taxi_trips_part.fare))), (PARTIAL count(*))
                    Sort Key: taxi_trips_part.payment_type
                    Sort Method: quicksort  Memory: 25kB
                    Buffers: shared hit=128 read=141969
                    Worker 0:  actual time=9714.984..9714.988 rows=1 loops=1
                      Sort Method: quicksort  Memory: 25kB
                      Buffers: shared hit=37 read=47628
                    Worker 1:  actual time=9688.712..9688.717 rows=2 loops=1
                      Sort Method: quicksort  Memory: 25kB
                      Buffers: shared hit=45 read=48316
                    ->  Partial HashAggregate  (cost=1011425.65..1011425.73 rows=8 width=31) (actual time=9709.959..9709.965 rows=5 loops=3)
                          Output: taxi_trips_part.payment_type, PARTIAL sum(taxi_trips_part.tips), PARTIAL sum((taxi_trips_part.tips + taxi_trips_part.fare)), PARTIAL count(*)
                          Group Key: taxi_trips_part.payment_type
                          Batches: 1  Memory Usage: 24kB
                          Buffers: shared hit=112 read=141969
                          Worker 0:  actual time=9714.866..9714.869 rows=1 loops=1
                            Batches: 1  Memory Usage: 24kB
                            Buffers: shared hit=29 read=47628
                          Worker 1:  actual time=9688.566..9688.571 rows=2 loops=1
                            Batches: 1  Memory Usage: 24kB
                            Buffers: shared hit=37 read=48316
                          ->  Parallel Append  (cost=0.56..872083.53 rows=11147370 width=23) (actual time=0.251..5983.820 rows=8917894 loops=3)
                                Buffers: shared hit=112 read=141969
                                Worker 0:  actual time=0.223..5852.389 rows=9667676 loops=1
                                  Buffers: shared hit=29 read=47628
                                Worker 1:  actual time=0.464..6029.348 rows=8750103 loops=1
                                  Buffers: shared hit=37 read=48316
                                ->  Parallel Index Only Scan using taxi_trips_part1_payment_type_tips_fare_idx1 on public.taxi_trips_part1 taxi_trips_part_1  (cost=0.56..499203.39 rows=7179947 width=21) (actual time=0.265..2950.562 rows=5743957 loops=3)
                                      Output: taxi_trips_part_1.payment_type, taxi_trips_part_1.tips, taxi_trips_part_1.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=69 read=84891
                                      Worker 0:  actual time=0.221..4167.949 rows=9667676 loops=1
                                        Buffers: shared hit=29 read=47628
                                      Worker 1:  actual time=0.294..2342.363 rows=4006815 loops=1
                                        Buffers: shared hit=20 read=19738
                                ->  Parallel Index Only Scan using taxi_trips_part2_payment_type_tips_fare_idx1 on public.taxi_trips_part2 taxi_trips_part_2  (cost=0.56..308282.66 rows=3843732 width=28) (actual time=0.250..2074.186 rows=4612478 loops=2)
                                      Output: taxi_trips_part_2.payment_type, taxi_trips_part_2.tips, taxi_trips_part_2.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=33 read=55576
                                      Worker 1:  actual time=0.462..2102.388 rows=4743288 loops=1
                                        Buffers: shared hit=17 read=28578
                                ->  Parallel Index Only Scan using taxi_trips_part10_payment_type_tips_fare_idx1 on public.taxi_trips_part10 taxi_trips_part_10  (cost=0.42..3020.55 rows=43279 width=24) (actual time=0.060..24.957 rows=103869 loops=1)
                                      Output: taxi_trips_part_10.payment_type, taxi_trips_part_10.tips, taxi_trips_part_10.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=1 read=514
                                ->  Parallel Index Only Scan using taxi_trips_part7_payment_type_tips_fare_idx1 on public.taxi_trips_part7 taxi_trips_part_7  (cost=0.42..2505.24 rows=35855 width=23) (actual time=0.026..23.539 rows=86053 loops=1)
                                      Output: taxi_trips_part_7.payment_type, taxi_trips_part_7.tips, taxi_trips_part_7.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=1 read=426
                                ->  Parallel Index Only Scan using taxi_trips_part4_payment_type_tips_fare_idx1 on public.taxi_trips_part4 taxi_trips_part_4  (cost=0.41..1785.93 rows=25523 width=23) (actual time=0.051..19.122 rows=61256 loops=1)
                                      Output: taxi_trips_part_4.payment_type, taxi_trips_part_4.tips, taxi_trips_part_4.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=1 read=304
                                ->  Parallel Index Only Scan using taxi_trips_part5_payment_type_tips_fare_idx1 on public.taxi_trips_part5 taxi_trips_part_5  (cost=0.29..930.43 rows=15467 width=26) (actual time=0.041..10.920 rows=26294 loops=1)
                                      Output: taxi_trips_part_5.payment_type, taxi_trips_part_5.tips, taxi_trips_part_5.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=1 read=160
                                ->  Parallel Index Only Scan using taxi_trips_part6_payment_type_tips_fare_idx1 on public.taxi_trips_part6 taxi_trips_part_6  (cost=0.29..424.01 rows=7985 width=22) (actual time=0.031..4.940 rows=13575 loops=1)
                                      Output: taxi_trips_part_6.payment_type, taxi_trips_part_6.tips, taxi_trips_part_6.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=1 read=68
                                ->  Parallel Index Only Scan using taxi_trips_part3_payment_type_tips_fare_idx1 on public.taxi_trips_part3 taxi_trips_part_3  (cost=0.28..181.18 rows=3292 width=24) (actual time=0.042..2.119 rows=5596 loops=1)
                                      Output: taxi_trips_part_3.payment_type, taxi_trips_part_3.tips, taxi_trips_part_3.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=1 read=29
                                ->  Parallel Index Only Scan using taxi_trips_part9_payment_type_tips_fare_idx1 on public.taxi_trips_part9 taxi_trips_part_9  (cost=0.14..10.10 rows=106 width=22) (actual time=0.142..0.173 rows=180 loops=1)
                                      Output: taxi_trips_part_9.payment_type, taxi_trips_part_9.tips, taxi_trips_part_9.fare
                                      Heap Fetches: 0
                                      Buffers: shared hit=1 read=1
                                ->  Parallel Seq Scan on public.taxi_trips_part11 taxi_trips_part_11  (cost=0.00..2.16 rows=16 width=25) (actual time=0.008..0.051 rows=27 loops=1)
                                      Output: taxi_trips_part_11.payment_type, taxi_trips_part_11.tips, taxi_trips_part_11.fare
                                      Buffers: shared hit=2
                                ->  Parallel Seq Scan on public.taxi_trips_part8 taxi_trips_part_8  (cost=0.00..1.04 rows=4 width=24) (actual time=0.056..0.083 rows=6 loops=1)
                                      Output: taxi_trips_part_8.payment_type, taxi_trips_part_8.tips, taxi_trips_part_8.fare
                                      Buffers: shared hit=1
                                ->  Parallel Seq Scan on public.table2_default taxi_trips_part_12  (cost=0.00..0.00 rows=1 width=48) (actual time=0.006..0.006 rows=0 loops=1)
                                      Output: taxi_trips_part_12.payment_type, taxi_trips_part_12.tips, taxi_trips_part_12.fare
Query Identifier: -1742866383571290105
Planning:
  Buffers: shared read=3
Planning Time: 0.499 ms
Execution Time: 9758.445 ms
```
### TYPE 
```
```

