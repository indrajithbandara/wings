#
#  Makefile --
#
#     Makefile for building Wings "accelerator" helpers.
#
#  Copyright (c) 2004-2013 Bjorn Gustavsson
#
#  See the file "license.terms" for information on usage and redistribution
#  of this file, and for a DISCLAIMER OF ALL WARRANTIES.
#
#     $Id: Makefile,v 1.11 2006/01/14 09:02:38 dgud Exp $
#
OS := $(shell uname -s)

ifeq ($(findstring CYGWIN,$(OS)),CYGWIN)
  USEMINGWGCC := true
  DEF_CC = mingw32-gcc
endif

ifdef MSYSTEM
 ifeq ($(MSYSTEM),$(filter $(MSYSTEM),MINGW32 MINGW64))
  USEMINGWGCC := true
  DEF_CC = gcc
 endif
endif

LIBDIR = ../../plugins/accel

ERL_DIR := $(shell echo 'io:format("~s~n",[code:root_dir()]),init:stop().' | erl | sed -n '/^1>/s/^1> //p')
ERL_INC = "$(ERL_DIR)/usr/include"
BEAM = $(LIBDIR)/wpc_pick.beam

UNIVERSAL_FLAGS =
COMMON_CFLAGS = -Wall -Werror -O3

ifdef USEMINGWGCC
 SO_EXT = dll
 LIBS = -shared
 GL_LIBS = -lopengl32 -lglu32
 CFLAGS = $(COMMON_CFLAGS)
else
 SO_EXT = so
 DEF_CC = $(CC)
 ifeq ($(OS),Darwin)
   CFLAGS = $(UNIVERSAL_FLAGS) $(COMMON_CFLAGS)
   LIBS = $(UNIVERSAL_FLAGS) -bundle -flat_namespace -undefined suppress
   GL_LIBS =
 else
   LIBS = -shared -fpic
   GL_LIBS = -L/usr/local/lib -lGLU -lGL
   CFLAGS = $(COMMON_CFLAGS)
 endif
endif

ifeq ($(GCC),)
    GCC = $(DEF_CC)
endif

ERLC = erlc
ERLC_FLAGS = -Werror -o $(LIBDIR) +debug_info

DRV = $(LIBDIR)/wings_pick_drv.$(SO_EXT)

TARGET_FILES=$(BEAM) $(DRV)

all: $(TARGET_FILES)

$(LIBDIR)/%.beam: %.erl
	install -d $(LIBDIR)
	$(ERLC) $(ERLC_FLAGS) $<

$(LIBDIR)/%.$(SO_EXT): %.c
	install -d $(LIBDIR)
	$(GCC) $(COMMON_CFLAGS) -I$(ERL_INC) -o $@ $< $(LIBS) $(GL_LIBS)

clean:
	rm -f $(TARGET_FILES)
	rm -f core
