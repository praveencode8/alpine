#
# Makefile for Don Libes' cgi.tcl - routines for writing CGI scripts in Tcl
#

VERSION = \"1.10.0\"
SHORT_VERSION = 1.10

srcdir = .

SUBDIRS = @subdirs@

######################################################################
# The following lines are things you may want to change
######################################################################

# By default, "make install" will install the appropriate files in
# /usr/local/bin, /usr/local/lib, /usr/local/man, etc.  By changing this
# variable, you can specify an installation prefix other than /usr/local.
# You may find it preferable to call configure with the --prefix option
# to control this information.  This is especially handy if you are
# installing this several times (perhaps on a number of machines or
# in different places).  Then you don't have to hand-edit this file.
# See the INSTALL file for more information.  (Analogous information
# applies to the next variable as well.)
prefix = /run/media/chappa/Work/alpine/alpinegit/web/lib

# You can specify a separate installation prefix for architecture-specific
# files such as binaries and libraries.
exec_prefix = ${prefix}

# Short directory path where binaries can be found to support #! hack.
# This directory path can be the same as the directory in which the binary
# actually sits except when the path is so long that the #! mechanism breaks
# (usually at 32 characters).
# The solution is to create a directory with a very short name, which consists
# only of symbolic links back to the true binaries.  Subtracting two for "#!"
# and a couple more for arguments (typically " -f" or " --") gives you 27
# characters.  Pathnames over this length won't be able to use the #! magic.
# For more info on this, see the execve(2) man page.
SHORT_BINDIR = $(exec_prefix)/bin

# Tcl interpreter for utility work.
CGI_TCL_EXECUTABLE = /usr/bin/tclsh

# Where to put the examples - a directory in which your web server has
# permission to execute CGI scripts.
exampledir = /tmp/cgi-bin/cgi-tcl-examples

######################################################################
# End of things you may want to change
#
# Do not change anything after this
######################################################################

bindir_arch_indep = $(prefix)/bin
libdir 	= $(exec_prefix)/lib
datadir = $(prefix)/lib

mandir 	= $(prefix)/man
man1dir = $(mandir)/man1
man3dir = $(mandir)/man3
docdir = $(datadir)/doc

# utility script directories - arch-independent and arch-non-
# independent.
SCRIPTDIR	= $(datadir)/cgi$(SHORT_VERSION)
EXECSCRIPTDIR	= $(execdatadir)/cgi$(SHORT_VERSION)

INSTALL = /usr/bin/install -c
INSTALL_PROGRAM = ${INSTALL}
INSTALL_DATA = ${INSTALL} -m 644

# To install the following examples: make examples
EXAMPLES=cookie.cgi creditcard.cgi \
	display.cgi display-in-frame.cgi download.cgi \
	echo.cgi error.cgi evaljs.cgi example.tcl examples.cgi \
	form-tour.cgi form-tour-result.cgi format-tour.cgi frame.cgi \
	image.cgi img.cgi kill.cgi nistguest.cgi oratcl.cgi \
	parray.cgi passwd.tcl passwd-form.cgi passwd.cgi \
	push.cgi rm.cgi stopwatch.cgi \
	unimail.cgi upload.cgi uploadbin.cgi \
	validate.cgi vclock.cgi vclock.pl vclock-src-frame.cgi visitor.cgi \
	vote.cgi

EXAMPLES_DATA=nistguest vote.cnt

all: cgi.tcl pkgIndex.tcl

info:
dvi:

# Delete all the installed files that the `install' target creates
# (but not the noninstalled files such as `make all' creates)
uninstall:
	-rm -f $(SCRIPTDIR)/cgi.tcl
	-rm -f $(man3dir)/cgi.tcl.3
	-rm -f $(SCRIPTDIR)/pkgIndex.tcl

.PHONY: install-info install info
install-info:

install: all
	${srcdir}/mkinstalldirs $(man3dir) $(SCRIPTDIR) $(exampledir) $(exampledir)/data
# install scripts
	$(INSTALL_DATA) cgi.tcl $(SCRIPTDIR)
# install library man page
	$(INSTALL_DATA) cgi.tcl.man $(man3dir)/cgi.tcl.3
	$(INSTALL_DATA) pkgIndex.tcl $(SCRIPTDIR)

examples:
	for i in $(EXAMPLES) ; do \
	  $(CGI_TCL_EXECUTABLE) $(srcdir)/fixline1 $(SHORT_BINDIR) < $(srcdir)/example/$$i > $$i ; \
	  $(INSTALL_PROGRAM) $$i $(exampledir) ; \
	  rm -f $$i ; \
	done
	for i in $(EXAMPLES_DATA) ; do \
	  $(INSTALL) -m 666 $(srcdir)/example/$$i $(exampledir)/data ; \
	done

cgi.tcl: $(srcdir)/cgi.tcl.in
	@echo "Rebuilding cgi.tcl..."
	$(SHELL) ./config.status

###################################
# Targets for Makefile and configure
###################################

Makefile: $(srcdir)/Makefile.in $(host_makefile_frag) config.status
	@echo "Rebuilding the Makefile..."
	$(SHELL) ./config.status

# Let "make -f Makefile.in" produce a configure file
configure: $(srcdir)/configure.in $(srcdir)/Makefile.in
	@echo "Rebuilding configure..."
	if [ x"${srcdir}" = x"." ] ; then \
	  srcdir=. ; export srcdir ; \
	else true ; fi ; \
	(cd $${srcdir}; autoconf)

config.status: $(srcdir)/configure
	@echo "Rebuilding config.status..."
	$(SHELL) ./config.status --recheck

check:
	@if [ -f testsuite/Makefile ]; then \
	   cd testsuite && $(MAKE) $(FLAGS_TO_PASS) check; \
	else true; fi

# updating of pkgIndex.tcl has not yet been totally automated
# Instructions:
# 1) Replace version # in cgi.tcl with updated version.
#    (Gad, I'd have to turn the whole source into .in file to automate this
#     and it'd be a major nuisance when developing!)
pkgIndex.tcl: cgi.tcl
	$(CGI_TCL_EXECUTABLE) pkgcreate

################################################
# Various "clean" targets follow GNU conventions
################################################

# delete all files from current directory that are created by "make"
clean:
	-rm -f *~ *.o core

# like "clean", but also delete files created by "configure"
distclean: clean
	-rm -f Makefile config.status config.cache config.log
	-rm -f Dbg_cf.h

# like "clean", but doesn't delete test utilities or massaged scripts
# because most people don't have to worry about them
mostlyclean:
	-rm -f *~ *.o core

# delete everything from current directory that can be reconstructed
# except for configure
realclean:	distclean

######################################
# Targets for pushing out releases
######################################

FTPDIR = /itl/www/div826/subject/expect/cgi.tcl

# make a private tar file for myself
tar:	cgi.tcl-$(SHORT_VERSION).tar
	mv cgi.tcl-$(SHORT_VERSION).tar cgi.tcl.tar

# Make a release and install it on ftp server
# Note that we run configure on our end to make sure that cgi.tcl is usable
# for people who don't have configure at the destination end (non-UNIX folks).
ftp:	cgi.tcl cgi.tcl-$(SHORT_VERSION).tar.Z cgi.tcl-$(SHORT_VERSION).tar.gz install-html
	cp cgi.tcl-$(SHORT_VERSION).tar.Z $(FTPDIR)/cgi.tcl.tar.Z
	cp cgi.tcl-$(SHORT_VERSION).tar.gz $(FTPDIR)/cgi.tcl.tar.gz
	cp HISTORY $(FTPDIR)
	cp README $(FTPDIR)/README.distribution
	cp doc/ref.txt $(FTPDIR)
	cp doc/ref.html $(FTPDIR)
	cp example/README $(FTPDIR)/example
	cp `pubfile example` $(FTPDIR)/example
	ls -l $(FTPDIR)/cgi.tcl.tar*
#	delete temp files
	rm cgi.tcl-$(SHORT_VERSION).tar*

cgi.tcl-$(SHORT_VERSION).tar: configure
	rm -f ../cgi.tcl-$(SHORT_VERSION)
	ln -s `pwd` ../cgi.tcl-$(SHORT_VERSION)
	cd ..;tar cvfh $@ `pubfile cgi.tcl-$(SHORT_VERSION)`
	mv ../$@ .

cgi.tcl-$(SHORT_VERSION).tar.Z:	cgi.tcl-$(SHORT_VERSION).tar
	compress -fc cgi.tcl-$(SHORT_VERSION).tar > $@

cgi.tcl-$(SHORT_VERSION).tar.gz:	cgi.tcl-$(SHORT_VERSION).tar
	gzip -fc cgi.tcl-$(SHORT_VERSION).tar > $@

###########################
# Targets for producing FAQ and homepage
###########################

NISTCGI_PREFIX = /local
NISTCGI_HOST = ats.nist.gov
NISTCGI_INTERPDIR = $(NISTCGI_PREFIX)/bin
NISTCGI_LIBDIR = $(NISTCGI_PREFIX)/lib/cgi$(SHORT_VERSION)
NISTCGI_EXAMPLEDIR = $(NISTCGI_HOST):/private/home/http/cgi-bin/cgi.tcl
NISTCGI_HTMLDIR = /itl/www/div826/subject/expect/cgi.tcl

# create the FAQ in html form
FAQ.html:	FAQ.src FAQ.tcl
	FAQ.src > FAQ.html

# create the FAQ in text form
#FAQ:	FAQ.src FAQ.tcl
#	FAQ.src text > FAQ

# generate home page
homepage.html:	homepage.src common.tcl configure
	homepage.src > homepage.html

realapps.html:	realapps.src common.tcl
	realapps.src > realapps.html

RSH = ssh
RCP = scp

# install various html docs on our web server
install-html:	FAQ.html homepage.html realapps.html pkgIndex.tcl cgi.tcl
	cp FAQ.html $(NISTCGI_HTMLDIR)/
	cp homepage.html $(NISTCGI_HTMLDIR)/index.html
	cp realapps.html $(NISTCGI_HTMLDIR)/
	-$(RSH) $(NISTCGI_HOST) mkdir $(NISTCGI_LIBDIR)/
	$(RCP) cgi.tcl pkgIndex.tcl $(NISTCGI_HOST):$(NISTCGI_LIBDIR)/
	for i in $(EXAMPLES) ; do \
	  $(CGI_TCL_EXECUTABLE) $(srcdir)/fixline1 $(NISTCGI_INTERPDIR) < $(srcdir)/example/$$i > $$i ; \
	  $(RCP) $$i $(NISTCGI_EXAMPLEDIR)/ ; \
	  rm -f $$i ; \
	done

# add recursive support to the build process.
subdir_do: force
	@for i in $(SUBDIRS); do \
	   echo "Making $(DO) in $${i}..." ; \
	   if [ -d ./$$i ] ; then \
	     if (rootme=`pwd`/ ; export rootme ; \
	       rootsrc=`cd $(srcdir); pwd`/ ; export rootsrc ; \
	       cd ./$$i; \
	       $(MAKE) $(FLAGS_TO_PASS) $(DO)) ; then true ; \
	     else exit 1 ; fi ; \
	   else true ; fi ; \
	done
force:

## dependencies will be put after this line... ##
