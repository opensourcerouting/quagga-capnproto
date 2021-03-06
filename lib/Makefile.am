## Process this file with automake to produce Makefile.in.

AM_CPPFLAGS = -I.. -I$(top_srcdir) -I$(top_srcdir)/lib -I$(top_srcdir)/lib/c-capnproto -I$(top_builddir)/lib
AM_CFLAGS = $(WERROR) $(ZEROMQ_CFLAGS)
DEFS = @DEFS@ -DSYSCONFDIR=\"$(sysconfdir)/\"

lib_LTLIBRARIES = libzebra.la
libzebra_la_LDFLAGS = -version-info 0:0:0 

libzebra_la_SOURCES = \
	network.c pid_output.c getopt.c getopt1.c daemon.c \
	checksum.c vector.c linklist.c vty.c command.c \
	sockunion.c prefix.c thread.c if.c buffer.c table.c hash.c \
	filter.c routemap.c distribute.c stream.c str.c log.c plist.c \
	zclient.c sockopt.c smux.c agentx.c snmp.c md5.c if_rmap.c keychain.c privs.c \
	sigevent.c pqueue.c jhash.c workqueue.c vrf.c \
	memory.c \
	memory_cli.c

libzebra_la_SOURCES += \
	c-capnproto/capn-malloc.c \
	c-capnproto/capn-stream.c \
	c-capnproto/capn.c

BUILT_SOURCES = route_types.h gitversion.h

libzebra_la_DEPENDENCIES = @LIB_REGEX@

libzebra_la_LIBADD = @LIB_REGEX@ @LIBCAP@ $(ZEROMQ_LIBS)

pkginclude_HEADERS = \
	buffer.h checksum.h command.h filter.h getopt.h hash.h \
	if.h linklist.h log.h \
	memory.h network.h prefix.h routemap.h distribute.h sockunion.h \
	str.h stream.h table.h thread.h vector.h version.h vty.h zebra.h \
	plist.h zclient.h sockopt.h smux.h md5.h if_rmap.h keychain.h \
	privs.h sigevent.h pqueue.h jhash.h zassert.h \
	workqueue.h route_types.h libospf.h vrf.h \
	memory_cli.h

noinst_HEADERS = \
	plist_int.h

noinst_HEADERS += \
	c-capnproto/capn.h

if HAVE_ZEROMQ
libzebra_la_SOURCES += qzmq.c qzc.c
pkginclude_HEADERS += qzmq.h qzc.h
endif

$(top_srcdir)/lib/c-capnproto/capnpc-c:
	make -C "$(top_srcdir)/lib/c-capnproto" capnpc-c
%.capnp.c: %.capnp $(top_srcdir)/lib/c-capnproto/capnpc-c
	$(AM_V_GEN) PATH="$(top_srcdir)/lib/c-capnproto:$(PATH)" capnp compile -oc $<
%.capnp.h: %.capnp.c
libzebra_la_SOURCES += qzc.capnp.c
BUILT_SOURCES += qzc.capnp.c

EXTRA_DIST = \
	qzc.capnp \
	regex.c regex-gnu.h \
	queue.h \
	route_types.pl route_types.txt \
	gitversion.pl

route_types.h: $(srcdir)/route_types.txt $(srcdir)/route_types.pl
	@PERL@ $(srcdir)/route_types.pl < $(srcdir)/route_types.txt > $@

if GIT_VERSION

# bit of a trick here to always have up-to-date git stamps without triggering
# unneccessary rebuilds.  .PHONY causes the .tmp file to be rebuilt always,
# but if we use that on gitversion.h it'll ripple through the .c file deps.
# (even if gitversion.h's file timestamp doesn't change, make will think it
# did, because of .PHONY...)

.PHONY: gitversion.h.tmp
.SILENT: gitversion.h gitversion.h.tmp
GITH=gitversion.h
gitversion.h.tmp: $(srcdir)/../.git
	@PERL@ $(srcdir)/gitversion.pl $(srcdir) > ${GITH}.tmp
gitversion.h: gitversion.h.tmp
	{ test -f ${GITH} && diff -s -q ${GITH}.tmp ${GITH}; } || cp -v ${GITH}.tmp ${GITH}

else
.PHONY: gitversion.h
gitversion.h:
	true
endif
