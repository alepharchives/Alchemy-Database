# Redis Makefile
# Copyright (C) 2009 Salvatore Sanfilippo <antirez at gmail dot com>
# This file is released under the BSD license, see the COPYING file

#LUAJIT= yes
#USE_TCMALLOC=yes #NOTE: not working right now
                  #BONUS: redis uses malloc, ALCHEMY_DATABASE uses tcmalloc :)

release_hdr := $(shell sh -c './mkreleasehdr.sh')
uname_S := $(shell sh -c 'uname -s 2>/dev/null || echo not')
OPTIMIZATION?=-O2
ifeq ($(uname_S),SunOS)
  @echo "Sun not supported (no BigEndian support) - sorry"
  @exit
endif
CFLAGS?= -std=c99 -pedantic $(OPTIMIZATION) -Wall -W $(ARCH) $(PROF) -I./ -I../../DB/ -DALCHEMY_DATABASE
CCLINK?= -lm -pthread -ldl
DEBUG?= -g -rdynamic -ggdb 

ifeq ($(USE_TCMALLOC),yes)
  CCLINK+= -ltcmalloc
  CFLAGS+= -DUSE_TCMALLOC
endif
CCOPT= $(CFLAGS) $(CCLINK) $(ARCH) $(PROF)

ifeq ($(LUAJIT),yes)
  LUAINC := "../deps/luajit/src/"
  LUALIB := "../deps/luajit/src/libluajit.a"
else
  LUAINC := "../deps/lua/src/"
  LUALIB := "../deps/lua/src/liblua.a"
endif
PREFIX= /usr/local
INSTALL_BIN= $(PREFIX)/bin
INSTALL= cp -p

OBJ = adlist.o ae.o anet.o dict.o redis.o sds.o zmalloc.o lzf_c.o lzf_d.o pqsort.o zipmap.o sha1.o ziplist.o release.o networking.o util.o object.o db.o replication.o rdb.o t_string.o t_list.o t_set.o t_zset.o t_hash.o config.o aof.o vm.o pubsub.o multi.o debug.o sort.o intset.o syncio.o scripting.o
BENCHOBJ = ae.o anet.o redis-benchmark.o sds.o adlist.o zmalloc.o
CLIOBJ = anet.o sds.o adlist.o redis-cli.o zmalloc.o release.o
CHECKDUMPOBJ = redis-check-dump.o lzf_c.o lzf_d.o
CHECKAOFOBJ = redis-check-aof.o
GENBENCHOBJ = ae.o anet.o gen-benchmark.o sds.o adlist.o zmalloc.o

PRGNAME = xdb-server
BENCHPRGNAME = xdb-benchmark
CLIPRGNAME = xdb-cli
CHECKDUMPPRGNAME = xdb-check-dump
CHECKAOFPRGNAME = xdb-check-aof
GENBENCHPRGNAME = xdb-gen-benchmark

all: x-dbb-lib redis-benchmark redis-cli redis-check-dump redis-check-aof redis-server xdb-gen-benchmark
	@echo ""
	@echo "Hint: To run 'make test' is a good idea ;)"
	@echo ""

# Deps (use make dep to generate this)
adlist.o: adlist.c adlist.h zmalloc.h
ae.o: ae.c ae.h zmalloc.h config.h ae_kqueue.c
ae_epoll.o: ae_epoll.c
ae_kqueue.o: ae_kqueue.c
ae_select.o: ae_select.c
anet.o: anet.c fmacros.h anet.h
aof.o: aof.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
chprgname.o: chprgname.c
config.o: config.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
db.o: db.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
debug.o: debug.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h sha1.h
dict.o: dict.c fmacros.h dict.h zmalloc.h
intset.o: intset.c intset.h zmalloc.h
lzf_c.o: lzf_c.c lzfP.h
lzf_d.o: lzf_d.c lzfP.h
multi.o: multi.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
networking.o: networking.c redis.h fmacros.h config.h ae.h sds.h dict.h \
  adlist.h zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
object.o: object.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
pqsort.o: pqsort.c
pubsub.o: pubsub.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
rdb.o: rdb.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h lzf.h
redis-benchmark.o: redis-benchmark.c fmacros.h ae.h anet.h sds.h adlist.h \
  zmalloc.h
redis-check-aof.o: redis-check-aof.c fmacros.h config.h
redis-check-dump.o: redis-check-dump.c lzf.h
redis-cli.o: redis-cli.c fmacros.h version.h sds.h adlist.h zmalloc.h
redis.o: redis.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
release.o: release.c release.h
replication.o: replication.c redis.h fmacros.h config.h ae.h sds.h dict.h \
  adlist.h zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
sds.o: sds.c sds.h zmalloc.h
sha1.o: sha1.c sha1.h
sort.o: sort.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h pqsort.h
syncio.o: syncio.c
t_hash.o: t_hash.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
t_list.o: t_list.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
t_set.o: t_set.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
t_string.o: t_string.c redis.h fmacros.h config.h ae.h sds.h dict.h \
  adlist.h zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
t_zset.o: t_zset.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
util.o: util.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
vm.o: vm.c redis.h fmacros.h config.h ae.h sds.h dict.h adlist.h \
  zmalloc.h anet.h zipmap.h ziplist.h intset.h version.h
ziplist.o: ziplist.c zmalloc.h ziplist.h
zipmap.o: zipmap.c zmalloc.h
zmalloc.o: zmalloc.c config.h

dependencies:
	cd ../deps/hiredis && $(MAKE) static ARCH="$(ARCH)"
	cd ../deps/linenoise && $(MAKE) ARCH="$(ARCH)"
	cd ../deps/lua && $(MAKE) ARCH="$(ARCH)" ansi
	cd ../deps/luajit && $(MAKE)

redis-server: $(OBJ)
	$(CC) -o $(PRGNAME) $(CCOPT) $(DEBUG) $(OBJ) $(LUALIB) -L../../DB/ -lx_db

redis-benchmark: dependencies $(BENCHOBJ)
	cd ../deps/hiredis && $(MAKE) static
	$(CC) -o $(BENCHPRGNAME) $(CCOPT) $(DEBUG) $(BENCHOBJ) ../deps/hiredis/libhiredis.a

redis-benchmark.o:
	$(CC) -c $(CFLAGS) -I../deps/hiredis $(DEBUG) $(COMPILE_TIME) $<

redis-cli: dependencies $(CLIOBJ)
	$(CC) -o $(CLIPRGNAME) $(CCOPT) $(DEBUG) $(CLIOBJ) ../deps/hiredis/libhiredis.a ../deps/linenoise/linenoise.o ../../DB/xdb_client_hooks.o

redis-cli.o:
	$(CC) -c $(CFLAGS) -I../deps/hiredis -I../deps/linenoise $(DEBUG) $(COMPILE_TIME) $<

redis-check-dump: $(CHECKDUMPOBJ)
	$(CC) -o $(CHECKDUMPPRGNAME) $(CCOPT) $(DEBUG) $(CHECKDUMPOBJ)

redis-check-aof: $(CHECKAOFOBJ)
	$(CC) -o $(CHECKAOFPRGNAME) $(CCOPT) $(DEBUG) $(CHECKAOFOBJ)

xdb-gen-benchmark: $(GENBENCHOBJ)
	$(CC) -o $(GENBENCHPRGNAME) $(CCOPT) $(DEBUG) $(GENBENCHOBJ)

x-dbb-lib:
	(cd ../../DB; make all)

.c.o:
	$(CC) -c $(CFLAGS) $(DEBUG) $(COMPILE_TIME) -I$(LUAINC) $<

clean:
	(cd ../../DB; make clean)
	rm -rf $(PRGNAME) $(BENCHPRGNAME) $(CLIPRGNAME) $(CHECKDUMPPRGNAME) $(CHECKAOFPRGNAME) *.o *.gcda *.gcno *.gcov

dep:
	$(CC) -MM *.c

test: redis-server
	(cd ..; tclsh8.5 tests/test_helper.tcl --tags "${TAGS}" --file "${FILE}")

bench:
	./redis-benchmark

log:
	git log '--pretty=format:%ad %s (%cn)' --date=short > ../Changelog

32bit:
	@echo ""
	@echo "WARNING: if it fails under Linux you probably need to install libc6-dev-i386"
	@echo ""
	$(MAKE) ARCH="-m32"

gprof:
	$(MAKE) PROF="-pg"

gcov:
	$(MAKE) PROF="-fprofile-arcs -ftest-coverage"

noopt:
	$(MAKE) OPTIMIZATION=""

32bitgprof:
	$(MAKE) PROF="-pg" ARCH="-arch i386"

install: all
	mkdir -p $(INSTALL_BIN)
	$(INSTALL) $(PRGNAME) $(INSTALL_BIN)
	$(INSTALL) $(BENCHPRGNAME) $(INSTALL_BIN)
	$(INSTALL) $(CLIPRGNAME) $(INSTALL_BIN)
	$(INSTALL) $(CHECKDUMPPRGNAME) $(INSTALL_BIN)
	$(INSTALL) $(CHECKAOFPRGNAME) $(INSTALL_BIN)
