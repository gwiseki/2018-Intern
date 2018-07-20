whiper applications : ycsb,tpcc,echo,redis,ctree,hashmap,memcached,vacation,nfs,exim,sql
This is really beginning version. I will update gradually.

# 1. whisper Download
===========================================================================
git clone --recursive https://github.com/swapnilh/whisper.git

-> successful

# 2. build whisper applications
===========================================================================
## ycsb : error

error message : 

make  all-recursive
make[1]: 디렉터리 '/home/hk/whisper/nstore' 들어감
Making all in src
make[2]: 디렉터리 '/home/hk/whisper/nstore/src' 들어감
g++ -DHAVE_CONFIG_H -I. -I..  -I./common -Wno-pointer-arith   -Wall -Wextra -Werror  -ggdb -O3 -D_ENABLE_FTRACE -MT libpm.o -MD -MP -MF .deps/libpm.Tpo -c -o libpm.o libpm.cpp
libpm.cpp:9:31: error: dynamic exception specifications are deprecated in C++11 [-Werror=deprecated]
 void* operator new(size_t sz) throw (std::bad_alloc) {
                               ^~~~~
libpm.cpp:37:38: error: dynamic exception specifications are deprecated in C++11 [-Werror=deprecated]
 void *operator new[](std::size_t sz) throw (std::bad_alloc) {
                                      ^~~~~
cc1plus: all warnings being treated as errors
make[2]: *** [Makefile:466: libpm.o] 오류 1
make[2]: 디렉터리 '/home/hk/whisper/nstore/src' 나감
make[1]: *** [Makefile:413: all-recursive] 오류 1
make[1]: 디렉터리 '/home/hk/whisper/nstore' 나감
make: *** [Makefile:345: all] 오류 2

## tpcc : error

error message :

[hk@mangalyaan whisper]$ ./script.py -b -w 'tpcc'

>>> make

make  all-recursive
make[1]: 디렉터리 '/home/hk/whisper/nstore' 들어감
Making all in src
make[2]: 디렉터리 '/home/hk/whisper/nstore/src' 들어감
g++ -DHAVE_CONFIG_H -I. -I..  -I./common -Wno-pointer-arith   -Wall -Wextra -Werror  -ggdb -O3 -D_ENABLE_FTRACE -MT libpm.o -MD -MP -MF .deps/libpm.Tpo -c -o libpm.o libpm.cpp
libpm.cpp:9:31: error: dynamic exception specifications are deprecated in C++11 [-Werror=deprecated]
 void* operator new(size_t sz) throw (std::bad_alloc) {
                               ^~~~~
libpm.cpp:37:38: error: dynamic exception specifications are deprecated in C++11 [-Werror=deprecated]
 void *operator new[](std::size_t sz) throw (std::bad_alloc) {
                                      ^~~~~
cc1plus: all warnings being treated as errors
make[2]: *** [Makefile:466: libpm.o] 오류 1
make[2]: 디렉터리 '/home/hk/whisper/nstore/src' 나감
make[1]: *** [Makefile:413: all-recursive] 오류 1
make[1]: 디렉터리 '/home/hk/whisper/nstore' 나감
make: *** [Makefile:345: all] 오류 2

## echo : error

error message : too many error messages.

## redis : error

error message : too many error messages.

## ctree : error

error message : too many error messages.

## hashmap : error

error message : too many error messages.

## memcached : error

error message : many error messages.

## vacation : error

error message : 

In file included from build/library/mtm/include/config.h:36,
                 from build/library/mtm/src/config.c:35:
library/common/config_generic.h:37:10: fatal error: libconfig.h: No such file or directory
 #include <libconfig.h>
          ^~~~~~~~~~~~~
compilation terminated.
scons: *** [build/library/mtm/src/config.os] Error 1
scons: building terminated because of errors.

## nfs, exim, sql : error

error message : please visit github.com/snalli/PMFS-new
