# ----------------------------------------------------------------------------
#
#  Copyright (C) 2006-2010 Fons Adriaensen <fons@kokkinizita.net>
#
#  This program is free software; you can redistribute it and/or modify
#  it under the terms of the GNU General Public License as published by
#  the Free Software Foundation; either version 2 of the License, or
#  (at your option) any later version.
#
#  This program is distributed in the hope that it will be useful,
#  but WITHOUT ANY WARRANTY; without even the implied warranty of
#  MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#  GNU General Public License for more details.
#
#  You should have received a copy of the GNU General Public License
#  along with this program; if not, write to the Free Software
#  Foundation, Inc., 675 Mass Ave, Cambridge, MA 02139, USA.
#
# ----------------------------------------------------------------------------


PREFIX = /usr
SUFFIX := $(shell uname -m | sed -e 's/^unknown/$//' -e 's/^i.86/$//' -e 's/^x86_64/$/64/')
LIBDIR = lib$(SUFFIX)
SHARED = $(PREFIX)/share
VERSION = 0.4.4
DISTDIR = ambdec-$(VERSION)
CPPFLAGS += -O2 -ffast-math -Wall -MMD -MP -DVERSION=\"$(VERSION)\" -DSHARED=\"$(SHARED)\"
CPPFLAGS += -march=native


all:	ambdec ambdec_cli


AMBDEC_O = ambdec.o styles.o mainwin.o typewin.o confwin.o textmat.o \
	radbut.o filewin.o jclient.o nffilt.o xover2.o decoder.o adconf.o \
	png2img.o meter.o

ambdec:	CPPFLAGS += -I/usr/X11R6/include `freetype-config --cflags`
ambdec:	LDLIBS += -lclxclient -lclthreads -ljack -lpng -lXft -lX11 -lrt
ambdec:	LDFLAGS += -L/usr/X11R6/lib
ambdec:	$(AMBDEC_O)
	g++ $(LDFLAGS) -o $@ $(AMBDEC_O) $(LDLIBS)

$(AMBDEC_O):
-include $(AMBDEC_O:%.o=%.d)



AMBDEC_CLI_O = ambdec_cli.o jclient.o nffilt.o xover2.o decoder.o adconf.o

ambdec_cli:	LDLIBS += -lclthreads -ljack -lrt
ambdec_cli:	$(AMBDEC_CLI_O)
	g++ $(LDFLAGS) -o $@ $(AMBDEC_CLI_O) $(LDLIBS)

$(AMBDEC_CLI_O):
-include $(AMBDEC_CLI_O:%.o=%.d)



install:	all
	install -d $(DESTDIR)$(PREFIX)/bin
	install -m 755 ambdec     $(DESTDIR)$(PREFIX)/bin
	install -m 755 ambdec_cli $(DESTDIR)$(PREFIX)/bin
	install -d $(DESTDIR)$(SHARED)/ambdec
	install -m 644 ../share/* $(DESTDIR)$(SHARED)/ambdec


clean:
	/bin/rm -f *~ *.o *.a *.d *.so
	/bin/rm -f ambdec
	/bin/rm -f ambdec_cli

