AM_CPPFLAGS = \
	$(LUA_CFLAGS) \
	$(WFORCE_CFLAGS) \
	$(EXT_CFLAGS) \
	$(libsodium_CFLAGS) \
	$(GETDNS_CFLAGS) \
	$(JSON11_CFLAGS) \
	$(LIBHIREDIS_CFLAGS) \
	$(LIBCURL_CFLAGS) \
	$(LIBCRYPTO_INCLUDES) \
	$(LIBDROGON_INCLUDES) \
	$(LIBJSONCPP_INCLUDES) \
	$(LIBZ_INCLUDES) \
	$(LIBUUID_INCLUDES) \
	$(LIBPROMETHEUS_INCLUDES) \
	$(YAMLCPP_FLAGS) \
	$(MMDB_CFLAGS) \
	-DSYSCONFDIR='"$(sysconfdir)"' \
	$(SANITIZER_FLAGS)

AM_LDFLAGS = \
	$(PROGRAM_LDFLAGS) \
	$(THREADFLAGS) \
	$(SANITIZER_FLAGS) \
	$(BOOST_DATE_TIME_LDFLAGS) \
	$(BOOST_FILESYSTEM_LDFLAGS) \
	$(BOOST_REGEX_LDFLAGS) \
	$(BOOST_SYSTEM_LDFLAGS)

UAP_REGEX_FILE=regexes.yaml

EXTRA_DIST= wforce.conf wforce.service.in wforce.conf.example replication.proto $(UAP_REGEX_FILE)

CLEANFILES = replication.pb.cc replication.pb.h wforce.service $(UAP_REGEX_FILE)
distclean-local:
	-rm -rf *.dSYM

sysconf_DATA= wforce.conf wforce.conf.example $(UAP_REGEX_FILE)

bin_PROGRAMS =  wforce \
		wf_dump_entries

wforce_SOURCES = \
	wforce.cc wforce.hh \
	wforce-lua.cc \
	wforce-common-lua.cc wforce-common-lua.hh \
	wforce-web.cc wforce-web.hh\
	wforce-sibling.cc wforce-sibling.hh \
	twmap.cc \
	twmap-wrapper.cc twmap-wrapper.hh \
	blackwhitelist.cc blackwhitelist.hh \
	replication.cc replication.hh \
	replication_sdb.hh replication_sdb.cc \
	replication_bl.cc replication_bl.hh \
	replication_wl.cc replication_wl.hh \
	wforce-replication.cc wforce-replication.hh \
	wforce-prometheus.hh wforce-prometheus.cc \
	luastate.hh

wforce_LDFLAGS = \
	$(AM_LDFLAGS) \
	$(LIBCRYPTO_LDFLAGS) \
	$(LIBPROMETHEUS_LDFLAGS) \
	$(LIBDROGON_LDFLAGS) \
	$(LIBJSONCPP_LDFLAGS) \
	$(LIBZ_LDFLAGS) \
	$(LIBUUID_LDFLAGS)

wforce_LDADD = \
	-lreadline \
	$(LUA_LIBS) $(EXT_LIBS) $(WFORCE_LIBS) \
	${libsodium_LIBS} $(GEOIP_LIBS) $(MMDB_LIBS) $(GETDNS_LIBS)  $(PROTOBUF_LIBS) \
	$(LIBSYSTEMD_LIBS) $(JSON11_LIBS) $(BOOST_DATE_TIME_LIBS) \
	$(BOOST_REGEX_LIBS) $(LIBHIREDIS_LIBS) $(LIBDROGON_LIBS) \
	$(LIBJSONCPP_LIBS) $(LIBZ_LIBS) $(LIBUUID_LIBS) $(LIBCURL) $(LIBCRYPTO_LIBS) \
	$(YAMLCPP_LIBS) $(LIBPROMETHEUS_LIBS) $(LIBSSL_LIBS) $(DLLIBS) $(STDCXXFSLIBS)

EXTRA_wforce_DEPENDENCIES = \
	$(EXT_LIBS) $(WFORCE_LIBS)

wf_dump_entries_SOURCES = \
	dump_entries.cc

wf_dump_entries_LDFLAGS = \
	$(AM_LDFLAGS)

wf_dump_entries_LDADD = \
	$(WFORCE_LIBS) $(LIBCURL) ${libsodium_LIBS}

EXTRA_wf_dump_entries_DEPENDENCIES = \
	$(WFORCE_LIBS)

noinst_HEADERS = \
	blackwhitelist.hh \
	wforce.hh \
	replication.hh \
	replication_sdb.hh \
	replication_bl.hh \
	twmap-wrapper.hh \
	luastate.hh \
	wforce-web.hh \
	wforce-sibling.hh \
	wforce-replication.hh

BUILT_SOURCES = replication.pb.cc replication.pb.h
replication.pb.cc: replication.proto
	$(AM_V_GEN)$(PROTOC) -I $(srcdir) --cpp_out=. $<
replication.pb.h: replication.pb.cc

nodist_wforce_SOURCES = replication.pb.cc replication.pb.h

wforce.$(OBJEXT): replication.pb.cc

$(UAP_REGEX_FILE):
	wget -q https://raw.githubusercontent.com/ua-parser/uap-core/master/$(UAP_REGEX_FILE) -O $(UAP_REGEX_FILE)

if HAVE_SYSTEMD
SYSTEMD_TYPE = notify
systemdsystemunitdir = $(SYSTEMD_DIR)
systemdsystemunit_DATA = wforce.service
else
SYSTEMD_TYPE = simple
endif
wforce.service: wforce.service.in
	$(AM_V_GEN)sed -e 's![@]bindir[@]!$(bindir)!' -e 's![@]type[@]!$(SYSTEMD_TYPE)!' < $< > $@
