################################################################################
##
## Filename:	Makefile
##
## Project:	wbuart32, a full featured UART with simulator
##
## Purpose:	To direct the Verilator build of the Verilog portion of the
##		bench test.  The result is C++ code (built by Verilator), that
##	is then built (herein) into a library.
##
## ALTERNATE_PURPOSE:
##	All of the Verilog files within this directory may be made top level
##	files in their own right for the purpose of testing the UART capability
##	of your board.  Should you wish to test these as toplevel files, you
##	will need to remove the i_setup from the input, and set it to something
##	like:
##		wire	[29:0]	i_setup;
##
##		// If we have a 100MHz clock, then we can set up for a 115,200
##		// baud clock by setting i_setup to (100MHz / 115200) ~= 868.
##		// The upper bits of this number also set the protocol to
##		// one stop bit, no parity, and 8 data  bits.
##		assign	i_setup = 30'd868; 	// 115,200 Baud 8N1
##
##	Using this purpose, the UART ports of a new piece of hardware may be
##	proven.  To do this,
##		1. get BLINKY working first--to prove that the clock works like
##			you think it does.  Then, once BLINKY is running,
##		2. get helloworld working.  This requires only the clock and
##			the output UART pin to work.
##		(Aside) 3. Once helloworld works, you should be able to get
##			speechfifo to work with no further hassles.
##		4. After helloworld works, switch to getting linetest running on
##			your hardware.  This will prove that you have not only
##			the clock and output UART pin working, but that you also
##			have the input UART pin working as well.
##
## Targets:	The default target of this makefile, all, builds the target
##		test, which includes the linetest Verilator library, the
##	helloworld Verilator library, and the speechfifo Verilator library--all
##	necessary for bench testing using the C++ files in bench/cpp.
##
## Creator:	Dan Gisselquist, Ph.D.
##		Gisselquist Technology, LLC
##
################################################################################
##
## Copyright (C) 2015-2016, Gisselquist Technology, LLC
##
## This program is free software (firmware): you can redistribute it and/or
## modify it under the terms of  the GNU General Public License as published
## by the Free Software Foundation, either version 3 of the License, or (at
## your option) any later version.
##
## This program is distributed in the hope that it will be useful, but WITHOUT
## ANY WARRANTY; without even the implied warranty of MERCHANTIBILITY or
## FITNESS FOR A PARTICULAR PURPOSE.  See the GNU General Public License
## for more details.
##
## You should have received a copy of the GNU General Public License along
## with this program.  (It's in the $(ROOT)/doc directory, run make with no
## target there if the PDF file isn't present.)  If not, see
## <http://www.gnu.org/licenses/> for a copy.
##
## License:	GPL, v3, as defined and found on www.gnu.org,
##		http://www.gnu.org/licenses/gpl.html
##
################################################################################
##
##
all:	test
YYMMDD=`date +%Y%m%d`
CXX   := g++
FBDIR := .
VDIRFB:= $(FBDIR)/obj_dir
RTLDR := ../../rtl
VERILATOR := verilator
VFLAGS := -Wall --MMD --trace -y ../../rtl -cc

.PHONY: test testline testhello speechfifo
test: testline testhello speechfifo
testline:  $(VDIRFB)/Vlinetest__ALL.a
testhello: $(VDIRFB)/Vhelloworld__ALL.a
speechfifo: $(VDIRFB)/Vspeechfifo__ALL.a

$(VDIRFB)/Vlinetest__ALL.a: $(VDIRFB)/Vlinetest.h $(VDIRFB)/Vlinetest.cpp
$(VDIRFB)/Vlinetest__ALL.a: $(VDIRFB)/Vlinetest.mk
$(VDIRFB)/Vlinetest.h $(VDIRFB)/Vlinetest.cpp $(VDIRFB)/Vlinetest.mk: linetest.v
$(VDIRFB)/Vlinetest.h $(VDIRFB)/Vlinetest.cpp $(VDIRFB)/Vlinetest.mk: $(RTLDR)/rxuart.v $(RTLDR)/txuart.v
$(VDIRFB)/Vlinetest.h $(VDIRFB)/Vlinetest.cpp $(VDIRFB)/Vlinetest.mk: $(RTLDR)/rxuartlite.v $(RTLDR)/txuartlite.v

$(VDIRFB)/Vhelloworld__ALL.a: $(VDIRFB)/Vhelloworld.h $(VDIRFB)/Vhelloworld.cpp
$(VDIRFB)/Vhelloworld__ALL.a: $(VDIRFB)/Vhelloworld.mk
$(VDIRFB)/Vhelloworld.h $(VDIRFB)/Vhelloworld.cpp $(VDIRFB)/Vhelloworld.mk: helloworld.v
$(VDIRFB)/Vhelloworld.h $(VDIRFB)/Vhelloworld.cpp $(VDIRFB)/Vhelloworld.mk: $(RTLDR)/txuartlite.v $(RTLDR)/txuart.v

SPEECHSRCS := $(addprefix $(RTLDR)/,rxuart.v txuart.v rxuartlite.v txuartlite.v ufifo.v wbuart.v)
SPEECHVFILES:= $(addprefix $(VDIRFB)/,Vspeechfifo.h Vspeechfifo.cpp Vspeechfifo.mk)
$(VDIRFB)/Vspeechfifo__ALL.a: $(VDIRFB)/Vspeechfifo.h $(VDIRFB)/Vspeechfifo.cpp
$(VDIRFB)/Vspeechfifo__ALL.a: $(VDIRFB)/Vspeechfifo.mk
$(SPEECHVFILES): speechfifo.v $(SPEECHSRCS)

$(VDIRFB)/V%.cpp $(VDIRFB)/V%.h $(VDIRFB)/V%.mk: $(FBDIR)/%.v
	$(VERILATOR) $(VFLAGS) $*.v

$(VDIRFB)/V%__ALL.a: $(VDIRFB)/V%.mk
	cd $(VDIRFB); make -f V$*.mk

.PHONY: clean
clean:
	rm -rf $(VDIRFB)/*.mk
	rm -rf $(VDIRFB)/*.cpp
	rm -rf $(VDIRFB)/*.h
	rm -rf $(VDIRFB)/

DIRS := $(wildcard $(VDIRFB)/*.d)
ifneq ($(DIRS),)
-include $(DIRS)
endif
