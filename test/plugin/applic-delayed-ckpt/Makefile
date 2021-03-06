# To demonstrate, do:  make check    [Checkpoints every 5 seconds]

# The name will be the same as the current directory name.
NAME=${shell basename $$PWD}

# By default, your resulting library will have this name.
LIBNAME=dmtcp_plugin-to-announce-events

# As you add new files to your hijack library, add the object file names here.
LIBOBJS = plugin-to-announce-events.o

# Modify if your DMTCP_ROOT is located elsewhere.
ifndef DMTCP_ROOT
  DMTCP_ROOT=../../..
endif
DMTCP_INCLUDE=${DMTCP_ROOT}/include

CFLAGS += -I${DMTCP_INCLUDE} -DDMTCP -fPIC -c -g
CXXFLAGS += -I${DMTCP_INCLUDE} -DDMTCP -fPIC -c -g

DEMO_PORT=7781

default: ${LIBNAME}.so applic

applic: applic.c
	${CC} -fPIC -I${DMTCP_INCLUDE} -o $@ $< -ldl

check: ${LIBNAME}.so applic
	@ echo ""
	@ echo "============ TESTING ./applic _WITHOUT_ DMTCP ================"
	- ./applic
	@ echo '(Application has exited.)'
	@ echo ""
	@ sleep 3
	@ echo "============ TESTING ./applic WITH DMTCP ================="
	# -i 2 flag will write checkpoint every two seconds.
	${DMTCP_ROOT}/bin/dmtcp_launch --quiet --port ${DEMO_PORT} -i 2 \
	  --with-plugin $$PWD/${LIBNAME}.so ./applic &
	@ sleep 7
	@ echo ""
	@ echo "===== RESTARTING USING ./dmtcp_restart_script.sh ============="
	./dmtcp_restart_script.sh
	@ echo ""
	@ echo "ALL TESTS SUCCEEDED"

# We link the library using C++ for compatibility with the main libdmtcp.so
${LIBNAME}.so: ${LIBOBJS}
	${CXX} -shared -fPIC -o $@ $^

.c.o:
	${CC} ${CFLAGS} -o $@ $<
.cpp.o:
	${CXX} ${CXXFLAGS} -o $@ $<

tidy:
	rm -f *~ .*.swp dmtcp_restart_script*.sh ckpt_*.dmtcp

clean: tidy
	rm -f ${LIBOBJS} ${LIBNAME}.so applic

distclean: clean
	rm -f ${LIBNAME}.so *~ .*.swp dmtcp_restart_script*.sh ckpt_*.dmtcp

dist: distclean
	dir=`basename $$PWD`; cd ..; \
	  tar czvf $$dir.tar.gz --exclude-vcs ./$$dir
	dir=`basename $$PWD`; ls -l ../$$dir.tar.gz

.PHONY: default clean dist distclean
