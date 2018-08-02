Development Environment
```
OS : Fedora
Architecture: x86_64
Model name: Intel(R) Xeon(R) Silver 4116 CPU @ 2.10GHz
```
whiper applications : ycsb, tpcc, echo, redis, ctree, hashmap, memcached, vacation, nfs, exim, sql <br/>
This is really beginning version. I will update gradually.<br/>
And on same kinds of machine, the results can be different.

# Current

|         | ycsb(N-store) | tpcc(N-store) | echo(KVS) | redis(KVS) | C-tree | Hashmap |
|:-------:|:-------------:|:-------------:|:---------:|:----------:|:------:|:-------:|
| compile |       Y       |       Y       |     Y     |      Y     |    Y   |    Y    |
| execute |       Y       |       Y       |     Y     |      Y     |    Y   |    Y    |
|   etc   |               |               |           |            |        |         |

|         | memcached(KVS) | vacation(OLTP, KVS, Mnemosyne) | nfs(PMFS)  | exim(PMFS) | sql(PMFS) |
|:-------:|:--------------:|:------------------------------:|:----------:|:----------:|:---------:|
| compile |        N       |                N               |            |            |           |
| execute |        N       |                N               |Y(on ubuntu)|      N     |     N     |
|   etc   |    Excluded    |            Excluded            |            |            |           |

# 1. whisper Download
git clone --recursive https://github.com/swapnilh/whisper.git

-> successful

# 2. build whisper applications
## ycsb, tpcc : completed.

It may incur error message.<br/>
error message : dynamic exception specifications are deprecated in C++11 [-Werror=deprecated]<br/>
Resolve it by removing '-Werror' option in Makefile.<br/>

## memcached : error
```
error message :

(LINK)     build/library/pmalloc/libpmalloc.so
(LINK)     build/bench/memcached/memcached-1.2.4-mtm/libpvar.so
/bin/ld: cannot find -lalps
collect2: error: ld returned 1 exit status
scons: *** [build/bench/memcached/memcached-1.2.4-mtm/libpvar.so] Error 1
scons: building terminated because of errors.
```
or you encounter other kind of message like that header does not exit, visit
https://github.com/snalli/mnemosyne-gcc/tree/cfed43142cdcb5175f1b7c75cd6a922ce561060e<br/>

And maybe in /whisper/mnemosyne-gcc/usermode/library/pmalloc/include/alps,
install-deps -> cmake -> make.... or something. Now I encountered this error.
```
/home/hk/whisper/mnemosyne-gcc/usermode/library/pmalloc/include/alps/src/common/log.cc:30:10: fatal error: boost/utility/empty_deleter.hpp: No such file or directory
 #include <boost/utility/empty_deleter.hpp>
          ^~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
compilation terminated.
make[2]: *** [src/CMakeFiles/alps.dir/build.make:128: src/CMakeFiles/alps.dir/common/log.cc.o] 오류 1
make[1]: *** [CMakeFiles/Makefile2:554: src/CMakeFiles/alps.dir/all] 오류 2
make: *** [Makefile:163: all] 오류 2
[alps]$
```
If anyone knows how to fix this problem, please teach me.

## vacation : error
```
error message : 

(COMPILE)  build/bench/stamp-kozy/vacation/pvar.c
(LINK)     build/bench/stamp-kozy/vacation/libpvar.so
/bin/ld: cannot find -lalps
collect2: error: ld returned 1 exit status
scons: *** [build/bench/stamp-kozy/vacation/libpvar.so] Error 1
scons: building terminated because of errors.
```

## nfs, exim, sql

Use another program (do not need compile)

## echo, redis : completed(too much warning)

## ctree, hashmap : completed

# 3. Run whisper applications
## ycsb : executed well.
```
[whisper]$ ./script.py -r -z 'small' -w 'ycsb'

>>> ./run.sh --small --ycsb

num_txns: 1000
num_keys: 10000
opt_wal_enable
per_writes: 0.5
num_executors: 2
ycsb_benchmark
num_keys :: 5000
num_txns :: 500
num_exec :: 2
Initialization Mode
num_keys :: 5000
num_txns :: 500
num_exec :: 2
Initialization Mode
LOADING...
Finished :: 100
EXECUTING...
num_txns :: 500
num_txns :: 500
Finished :: 100
duration :: 6.627
duration :: 7.06
dur :0 :: 6.627
dur :1 :: 7.06
max dur :7.06
OPT_WAL :: Duration(s) : 0.01 Throughput  : 141643.06
TOTAL EPOCHS : 0
```
When error :
```
./run.sh: line 50:  5040 Aborted                 (core dumped) $sudo $time $bin -x1000 -k10000 -w -p0.5 -e2 $trace $var
```
If you see this message, "Nstore(actually ycsb, tpcc) create a file in /dev/shm (zfile for nstore and efile} which act as persistent memory pools. So does every application in WHISPER. The default size is 1GB. When space is low, the above error is thrown. We will make this a run time parameter in the future. For now, simply increase it by altering the PSEGMENT_RESERVED_REGION_SIZE in Nstore and Echo. You may find this variable by using grep, cscope or any indexing tool for browsing source code. Its location varies with application. Delete the old pool, recompile and re-run.(from READMD.md file in whisper dir)"
<br/><br/>
Result analysis : <In whisper/nstore/main.cpp> first 6 lines are the input by shell script(run.sh).

## tpcc : executed well.
```
[whisper]$ ./script.py -r -z 'small' -w 'tpcc'

>>> ./run.sh --small --tpcc

num_txns: 10000
num_keys: 1000
opt_wal_enable
per_writes: 0.2
num_executors: 2
tpcc_benchmark
LOADING...
Finished :: 100
EXECUTING...
Finished :: 100
dur :0 :: 1269.33
dur :1 :: 1162.68
max dur :1269.33
OPT_WAL :: Duration(s) : 1.27 Throughput  : 7878.15
[whisper]$
```
When error :
```
./run.sh: line 50: 15295 Segmentation fault      (core dumped) $sudo $time $bin -x10000 -k1000 -w -p0.2 -e2 $trace $var
```
If you see this message, "Nstore(actually ycsb, tpcc) create a file in /dev/shm (zfile for nstore and efile} which act as persistent memory pools. So does every application in WHISPER. The default size is 1GB. When space is low, the above error is thrown. We will make this a run time parameter in the future. For now, simply increase it by altering the PSEGMENT_RESERVED_REGION_SIZE in Nstore and Echo. You may find this variable by using grep, cscope or any indexing tool for browsing source code. Its location varies with application. Delete the old pool, recompile and re-run.(from READMD.md file in whisper dir)"
<br/><br/>


## ctree : executed well.

```
[whisper]$ sudo ./script.py -r -z 'small' -w 'ctree'

>>> ./run_ctree.sh --small

tracing disabled by user.
tracing disabled by user.
map_insert [1]
total-avg;ops-per-second;total-max;total-min;total-median;total-std-dev;latency-avg;latency-min;latency-max;latency-std-dev;threads;ops-per-thread;data-size;seed;repeats;type;seed;max-key;external-tx;alloc
0.043593;46979.715820;0.046375;0.040812;0.046375;0.002782;42571;11724;18025255;416775.000000;2;1024;128;0;1;ctree;2385006593;0;false;false
TOTAL EPOCH COUNT : 0, RUNTIME : 1507338 us
exiting main.
```
When error : <br/>
error message : removing file failed: Operation not permitted <br/>
Resolve it by just using 'sudo' instruction.
## hashmap : executed well.
```
[whisper]$ sudo ./script.py -r -z 'small' -w 'hashmap'

>>> ./run_hashmap.sh --small

tracing disabled by user.
tracing disabled by user.
map_insert [1]
total-avg;ops-per-second;total-max;total-min;total-median;total-std-dev;latency-avg;latency-min;latency-max;latency-std-dev;threads;ops-per-thread;data-size;seed;repeats;type;seed;max-key;external-tx;alloc
0.042597;48078.571466;0.046742;0.038452;0.046742;0.004145;41598;12001;21136400;472617.000000;2;1024;128;0;1;hashmap_tx;2385006593;0;false;false
TOTAL EPOCH COUNT : 0, RUNTIME : 1472571 us
exiting main.
```
When error : <br/>
error message : removing file failed: Operation not permitted <br/>
Resolve it by just using 'sudo' instruction.
## memcached : error
```
[whisper]$ ./script.py -r -z 'small' -w 'memcached'

\>>> ./run_memcache.sh

memcached: no process found
./run_memcache.sh: line 18: ./build/bench/memcached/memcached-1.2.4-mtm/memcached: No such file or directory

\>>> starting memslap client


\>>> ./run_memslap.sh --small

/home/hk/whisper/mnemosyne-gcc/usermode/bench/memcached/memslap: error while loading shared libraries: libevent-2.0.so.5: cannot open shared object file: No such file or directory
/home/hk/whisper/mnemosyne-gcc/usermode/bench/memcached/memslap: error while loading shared libraries: libevent-2.0.so.5: cannot open shared object file: No such file or directory
/home/hk/whisper/mnemosyne-gcc/usermode/bench/memcached/memslap: error while loading shared libraries: libevent-2.0.so.5: cannot open shared object file: No such file or directory
/home/hk/whisper/mnemosyne-gcc/usermode/bench/memcached/memslap: error while loading shared libraries: libevent-2.0.so.5: cannot open shared object file: No such file or directory

\>>> kill -s SIGKILL `pgrep memcached`

sh: line 0: kill: SIGKILL: invalid signal specification
[whisper]$
```

## exim, sql : error

error message : please visit github.com/snalli/PMFS-new

## nfs : executed.(imperfect - could not mount nfs)

- run fileserver-asplos.f with filebench (On Ubuntu)
```
gwak0320@ubuntu:~/whisper/PMFS-new/workloads/filsrv$ sudo filebench -f fileserver-asplos.f            
Filebench Version 1.5-alpha3
0.000: Allocated 173MB of shared memory
0.485: File-server Version 3.0 personality successfully loaded
0.486: Usage: set $dir=<dir>
0.487:        set $meanfilesize=<size>     defaults to 131072
0.487:        set $nfiles=<value>      defaults to 10000
0.488:        set $nthreads=<value>    defaults to 1
0.488:        set $meanappendsize=<value>  defaults to 16384
0.488:        set $iosize=<size>  defaults to 1048576
0.488:        set $meandirwidth=<size> defaults to 20
0.488:        run runtime (e.g. run 60)
0.488: Populating and pre-allocating filesets
0.563: bigfileset populated: 10000 files, avg. dir. width = 20, avg. dir. depth = 3.1, 0 leafdirs, 1250.000MB total size
0.565: Removing bigfileset tree (if exists)
2.633: Pre-allocating directories in bigfileset tree
2.672: Pre-allocating files in bigfileset tree
59.977: Waiting for pre-allocation to finish (in case of a parallel pre-allocation)
59.977: Population and pre-allocation of filesets completed
60.012: Starting 1 filereader instances
61.820: Running...
62.821: Run took 1 seconds...
62.822: Per-Operation Breakdown
statfile1            18ops       18ops/s   0.0mb/s      1.4ms/op [0.00ms - 25.58ms]
deletefile1          18ops       18ops/s   0.0mb/s      4.5ms/op [0.02ms - 44.84ms]
closefile3           18ops       18ops/s   0.0mb/s      0.0ms/op [0.00ms -  0.01ms]
readfile1            18ops       18ops/s   2.2mb/s     26.4ms/op [0.07ms - 58.47ms]
openfile2            19ops       19ops/s   0.0mb/s      8.7ms/op [0.00ms - 164.84ms]
closefile2           19ops       19ops/s   0.0mb/s      0.0ms/op [0.00ms -  0.00ms]
appendfilerand1      19ops       19ops/s   0.1mb/s      0.0ms/op [0.01ms -  0.03ms]
openfile1            19ops       19ops/s   0.0mb/s      0.0ms/op [0.01ms -  0.03ms]
closefile1           19ops       19ops/s   0.0mb/s      0.0ms/op [0.00ms -  0.00ms]
wrtfile1             19ops       19ops/s   2.4mb/s      0.1ms/op [0.07ms -  0.20ms]
createfile1          19ops       19ops/s   0.0mb/s      0.0ms/op [0.01ms -  0.08ms]
62.825: IO Summary:   205 ops 204.806 ops/s 18/38 rd/wr   4.8mb/s  13.4ms/op
62.825: Shutting down processes
gwak0320@ubuntu:~/whisper/PMFS-new/workloads/filsrv$
```

- run nfs-asplos.f with filebench (On Ubuntu)
```
gwak0320@ubuntu:~/whisper/PMFS-new/workloads/filsrv$ sudo filebench -f nfs-asplos.f
Filebench Version 1.5-alpha3
0.000: Allocated 173MB of shared memory
0.303: File-server Version 3.0 personality successfully loaded
0.304: Populating and pre-allocating filesets
0.363: bigfileset populated: 10000 files, avg. dir. width = 20, avg. dir. depth = 3.1, 0 leafdirs, 1250.000MB total size
0.365: Reusing existing bigfileset tree
0.366: Pre-allocating files in bigfileset tree
0.383: Waiting for pre-allocation to finish (in case of a parallel pre-allocation)
0.384: Population and pre-allocation of filesets completed
0.392: Starting 1 filereader instances
1.444: Running...
121.776: Run took 120 seconds...
122.011: Per-Operation Breakdown
statfile1            30396ops      253ops/s   0.0mb/s      0.0ms/op [0.00ms - 17.29ms]
deletefile1          30184ops      251ops/s   0.0mb/s     28.9ms/op [0.02ms - 2585.62ms]
closefile3           30404ops      253ops/s   0.0mb/s      0.0ms/op [0.00ms - 10.11ms]
readfile1            30404ops      253ops/s  33.4mb/s      0.2ms/op [0.00ms - 315.59ms]
openfile2            30404ops      253ops/s   0.0mb/s      0.0ms/op [0.00ms - 51.97ms]
closefile2           30404ops      253ops/s   0.0mb/s      0.0ms/op [0.00ms - 17.54ms]
appendfilerand1      30404ops      253ops/s   2.0mb/s      0.2ms/op [0.00ms - 358.76ms]
openfile1            30404ops      253ops/s   0.0mb/s      0.0ms/op [0.00ms - 47.32ms]
closefile1           30404ops      253ops/s   0.0mb/s      0.0ms/op [0.00ms - 27.39ms]
wrtfile1             30404ops      253ops/s  31.6mb/s      0.6ms/op [0.05ms - 363.77ms]
createfile1          30404ops      253ops/s   0.0mb/s      1.7ms/op [0.01ms - 529.60ms]
122.011: IO Summary: 334216 ops 2777.607 ops/s 253/505 rd/wr  67.0mb/s  10.5ms/op
122.011: Shutting down processes
gwak0320@ubuntu:~/whisper/PMFS-new/workloads/filsrv$
```
## echo : executed well.
```
[whisper]$ ./script.py -r -z 'med' -w 'echo'

>>> ./run.sh --med

got arguments: CPUS=2
,iterations=16
, key_size=128
, value_size=1024
, merge_every=8
, put_probability=0.800000
, update_probability=0.700000
, num_threads=2
 operations=100000

Size of PM pool:1073741824
maximum number of keys printable is 1844674407
KP: 832: Using 16 for local hash table size (same as merge frequency), 10208 for master hash table size
KP: 832: Total memory allocation size (just evaluation, not kv stores themselves) could be up to 0 bytes (0 MB)
Will create 240 random integers for each child thread
Now starting evaluation for key-value store: kp_kvstore
On 2 cores
Running on storage platform: disk
Starting multi-threaded tests: num_threads=2, NUM_CPUS=2
0.200000 GETS
KP: 832: pinned new thread to CPU=0x0
KP: 832: pinned new thread to CPU=0x1
KP: 1824: actually starting now
KP: 9120: actually starting now
For multithreaded run
         200000 total ops (161552 puts + 38448 gets)
         981647 in runtime (usecs) = 0.203739 ops/usec
         168522144.000000 byte/sec put throughput (this thread)
         40106832.000000 byte/sec get throughput (this thread)
         208628960.000000 byte/sec total throughput (this thread)
         Conflicts in worker thread: 0 (of 20195 commits)
         Errors in worker thread: 0
***END OF RAMP-UP-THREADS OUTPUT***

***CONCLUDING THREAD EVALUATION***
5.88user 4.07system 0:07.12elapsed 139%CPU (0avgtext+0avgdata 1101740maxresident)k
0inputs+0outputs (0major+276529minor)pagefaults 0swaps
[whisper]$
```
When Error :
```
Unable to allocate memory pool<br/>
```
If you see this message, Resolve it by just using 'sudo' instruction.
<br/><br/>
```
no free memory of size 128 available
```
If you see this message, "Echo create a file in /dev/shm (zfile for nstore and efile for echo} which act as persistent memory pools. So does every application in WHISPER. The default size is 1GB. When space is low, the above error is thrown. We will make this a run time parameter in the future. For now, simply increase it by altering the PSEGMENT_RESERVED_REGION_SIZE in Nstore and Echo. You may find this variable by using grep, cscope or any indexing tool for browsing source code. Its location varies with application. Delete the old pool, recompile and re-run.(from READMD.md file in whisper dir)"
<br/><br/>
## redis : executed.
```
[whisper]$ sudo ./script.py -r -z 'large' -w 'redis'

>>> ./run-redis-server.sh

tracing disabled by user.
42957:C 01 Aug 14:59:35.904 * Start init Persistent memory file /dev/shm/redis.pm size 2.00G
42957:C 01 Aug 14:59:35.913 * Init Persistent memory file /dev/shm/redis.pm size 2.00G time 0.009 seconds
42957:M 01 Aug 14:59:35.914 * Increased maximum number of open files to 10032 (it was originally set to 1024).
                _._
           _.-``__ ''-._
      _.-``    `.  `_.  ''-._           Redis 3.1.103_NVML (4a5beee5/0) 64 bit
  .-`` .-```.  ```\/    _.,_ ''-._
 (    '      ,       .-`  | `,    )     Running in standalone mode
 |`-._`-...-` __...-.``-._|'` _.-'|     Port: 6379
 |    `-._   `._    /     _.-'    |     PID: 42957
  `-._    `-._  `-./  _.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |           http://redis.io
  `-._    `-._`-.__.-'_.-'    _.-'
 |`-._`-._    `-.__.-'    _.-'_.-'|
 |    `-._`-._        _.-'_.-'    |
  `-._    `-._`-.__.-'_.-'    _.-'
      `-._    `-.__.-'    _.-'
          `-._        _.-'
              `-.__.-'

42957:M 01 Aug 14:59:35.917 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
42957:M 01 Aug 14:59:35.917 # Server started, Redis version 3.1.103_NVML
42957:M 01 Aug 14:59:35.918 # WARNING you have Transparent Huge Pages (THP) support enabled in your kernel. This will create latency and memory usage issues with Redis. To fix this issue run the command 'echo never > /sys/kernel/mm/transparent_hugepage/enabled' as root, and add it to your /etc/rc.local in order to retain the setting after a reboot. Redis must be restarted after THP is disabled.
42957:M 01 Aug 14:59:35.918 * The server is now ready to accept connections on port 6379

>>> starting redis client


>>> ./run-redis-cli.sh --large

58750 Gets/sec | Hits: 6115 (10.41%) | Misses: 52635 (89.59%)
59750 Gets/sec | Hits: 16544 (27.69%) | Misses: 43206 (72.31%)
61750 Gets/sec | Hits: 25550 (41.38%) | Misses: 36200 (58.62%)
61000 Gets/sec | Hits: 31385 (51.45%) | Misses: 29615 (48.55%)
64000 Gets/sec | Hits: 38089 (59.51%) | Misses: 25911 (40.49%)
64500 Gets/sec | Hits: 42414 (65.76%) | Misses: 22086 (34.24%)
65000 Gets/sec | Hits: 46416 (71.41%) | Misses: 18584 (28.59%)
65250 Gets/sec | Hits: 48947 (75.01%) | Misses: 16303 (24.99%)
65250 Gets/sec | Hits: 50928 (78.05%) | Misses: 14322 (21.95%)
59750 Gets/sec | Hits: 48142 (80.57%) | Misses: 11608 (19.43%)
66000 Gets/sec | Hits: 54528 (82.62%) | Misses: 11472 (17.38%)
64500 Gets/sec | Hits: 54341 (84.25%) | Misses: 10159 (15.75%)
54000 Gets/sec | Hits: 46139 (85.44%) | Misses: 7861 (14.56%)
54000 Gets/sec | Hits: 46695 (86.47%) | Misses: 7305 (13.53%)
55000 Gets/sec | Hits: 48219 (87.67%) | Misses: 6781 (12.33%)
54500 Gets/sec | Hits: 48223 (88.48%) | Misses: 6277 (11.52%)
54500 Gets/sec | Hits: 48612 (89.20%) | Misses: 5888 (10.80%)
55000 Gets/sec | Hits: 49399 (89.82%) | Misses: 5601 (10.18%)
54750 Gets/sec | Hits: 49434 (90.29%) | Misses: 5316 (9.71%)
....(repetition)
....
55250 Gets/sec | Hits: 53272 (96.42%) | Misses: 1978 (3.58%)
55250 Gets/sec | Hits: 53376 (96.61%) | Misses: 1874 (3.39%)
54000 Gets/sec | Hits: 52235 (96.73%) | Misses: 1765 (3.27%)
54750 Gets/sec | Hits: 52924 (96.66%) | Misses: 1826 (3.34%)
55250 Gets/sec | Hits: 53430 (96.71%) | Misses: 1820 (3.29%)
55250 Gets/sec | Hits: 53375 (96.61%) | Misses: 1875 (3.39%)
54000 Gets/sec | Hits: 52337 (96.92%) | Misses: 1663 (3.08%)
55000 Gets/sec | Hits: 53369 (97.03%) | Misses: 1631 (2.97%)
55000 Gets/sec | Hits: 53403 (97.10%) | Misses: 1597 (2.90%)
55000 Gets/sec | Hits: 53414 (97.12%) | Misses: 1586 (2.88%)
54000 Gets/sec | Hits: 52431 (97.09%) | Misses: 1569 (2.91%)
55000 Gets/sec | Hits: 53489 (97.25%) | Misses: 1511 (2.75%)
55250 Gets/sec | Hits: 53753 (97.29%) | Misses: 1497 (2.71%)
55000 Gets/sec | Hits: 53510 (97.29%) | Misses: 1490 (2.71%)

>>> kill -s SIGKILL `pgrep redis`

sh: line 0: kill: SIGKILL: invalid signal specification
[whisper]$
```
Simulating a cache workload with an 80-20 distribution(--lru-test option). The detailed opeation is the function 'LRUTestMode' in redis-cli.c. That function has so many inner user-defined functions, so it is difficult to analyze. But simply it performs cycles of 1 second with 50% writes and 50% reads. I will keep analyzing it. <br/><br/>
When error :
```
[whisper]$ ./script.py -r -z 'small' -w 'redis'

\>>> ./run-redis-server.sh

failed to enable tracing. err = -1
41188:C 21 Jul 15:26:20.064 * Start init Persistent memory file /dev/shm/redis.pm size 2.00G
41188:C 21 Jul 15:26:20.064 # Cannot int persistent memory file /dev/shm/redis.pm size 2.00G
```
If you see this message, kill redis-server cilent process. ("ps -ef" and "kill -9")<br/><br/>
```
40652:M 01 Aug 14:23:41.919 # Server can't set maximum open files to 10032 because of OS error: Operation not permitted.
```
If you see this message, please execute in root client(sudo).<br/><br/>

```
41840:M 01 Aug 14:40:56.656 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
```
If you see this message, just do what they say.<br/><br/>




------------------------------------------------------------
If you have anything not understood, please ask to gwak0320@gmail.com.
------------------------------------------------------------
-- What I did last week (07.23.18)
- (Ongoing) Read paper on Whisper
- (Ongoing) Build and Execute applications in Whisper benchmark suite
- (Ongoing) Analyze the result of application(redis) which can be executed, and the cause of application(others) which cannot be executed with Dongui

-- What I will do for next two weeks
- The things which are ongoing on last week
- Execute applications in Whisper on memory X and analyze the result
--------------------------------------------------------------
-- What I did last week (07.30.18)
- (Ongoing) Build and Execute applications in Whisper benchmark suite and benchIT benchmark
- (Ongoing) Analyze the result of application(redis) which can be executed, and the cause of application(others) which cannot be executed with Dongui

-- What I will do for next two weeks
- The things which were ongoing on last week
- Execute applications in Whisper on memory X and analyze the result
