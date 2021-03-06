# Copyright (c) 2020 Alex Forencich
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sublicense, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN
# THE SOFTWARE.

TOPLEVEL_LANG = verilog

SIM ?= icarus
WAVES ?= 0

COCOTB_HDL_TIMEUNIT = 1ns
COCOTB_HDL_TIMEPRECISION = 1ps

DUT      = axil_register
TOPLEVEL = $(DUT)
MODULE   = test_$(DUT)
VERILOG_SOURCES += ../../rtl/$(DUT).v
VERILOG_SOURCES += ../../rtl/$(DUT)_wr.v
VERILOG_SOURCES += ../../rtl/$(DUT)_rd.v

REG_TYPE ?= 1

# module parameters
export PARAM_DATA_WIDTH ?= 32
export PARAM_ADDR_WIDTH ?= 32
export PARAM_STRB_WIDTH ?= $(shell expr $(PARAM_DATA_WIDTH) / 8 )
export PARAM_AW_REG_TYPE ?= $(REG_TYPE)
export PARAM_W_REG_TYPE ?= $(REG_TYPE)
export PARAM_B_REG_TYPE ?= $(REG_TYPE)
export PARAM_AR_REG_TYPE ?= $(REG_TYPE)
export PARAM_R_REG_TYPE ?= $(REG_TYPE)

SIM_BUILD ?= sim_build_$(MODULE)-$(PARAM_DATA_WIDTH)-$(REG_TYPE)

ifeq ($(SIM), icarus)
	PLUSARGS += -fst

	COMPILE_ARGS += -P $(TOPLEVEL).DATA_WIDTH=$(PARAM_DATA_WIDTH)
	COMPILE_ARGS += -P $(TOPLEVEL).ADDR_WIDTH=$(PARAM_ADDR_WIDTH)
	COMPILE_ARGS += -P $(TOPLEVEL).STRB_WIDTH=$(PARAM_STRB_WIDTH)
	COMPILE_ARGS += -P $(TOPLEVEL).AW_REG_TYPE=$(PARAM_AW_REG_TYPE)
	COMPILE_ARGS += -P $(TOPLEVEL).W_REG_TYPE=$(PARAM_W_REG_TYPE)
	COMPILE_ARGS += -P $(TOPLEVEL).B_REG_TYPE=$(PARAM_B_REG_TYPE)
	COMPILE_ARGS += -P $(TOPLEVEL).AR_REG_TYPE=$(PARAM_AR_REG_TYPE)
	COMPILE_ARGS += -P $(TOPLEVEL).R_REG_TYPE=$(PARAM_R_REG_TYPE)

	ifeq ($(WAVES), 1)
		VERILOG_SOURCES += iverilog_dump.v
		COMPILE_ARGS += -s iverilog_dump
	endif
else ifeq ($(SIM), verilator)
	COMPILE_ARGS += -Wno-SELRANGE -Wno-WIDTH

	COMPILE_ARGS += -GDATA_WIDTH=$(PARAM_DATA_WIDTH)
	COMPILE_ARGS += -GADDR_WIDTH=$(PARAM_ADDR_WIDTH)
	COMPILE_ARGS += -GSTRB_WIDTH=$(PARAM_STRB_WIDTH)
	COMPILE_ARGS += -GAW_REG_TYPE=$(PARAM_AW_REG_TYPE)
	COMPILE_ARGS += -GW_REG_TYPE=$(PARAM_W_REG_TYPE)
	COMPILE_ARGS += -GB_REG_TYPE=$(PARAM_B_REG_TYPE)
	COMPILE_ARGS += -GAR_REG_TYPE=$(PARAM_AR_REG_TYPE)
	COMPILE_ARGS += -GR_REG_TYPE=$(PARAM_R_REG_TYPE)

	ifeq ($(WAVES), 1)
		COMPILE_ARGS += --trace-fst
	endif
endif

iverilog_dump.v:
	echo 'module iverilog_dump();' > $@
	echo 'initial begin' >> $@
	echo '    $$dumpfile("$(TOPLEVEL).fst");' >> $@
	echo '    $$dumpvars(0, $(TOPLEVEL));' >> $@
	echo 'end' >> $@
	echo 'endmodule' >> $@

clean::
	@rm -rf sim_build_*
	@rm -rf iverilog_dump.v
	@rm -rf dump.fst $(TOPLEVEL).fst

include $(shell cocotb-config --makefiles)/Makefile.sim

