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

DUT      = eth_mac_mii_fifo
TOPLEVEL = $(DUT)
MODULE   = test_$(DUT)
VERILOG_SOURCES += ../../rtl/$(DUT).v
VERILOG_SOURCES += ../../rtl/eth_mac_mii.v
VERILOG_SOURCES += ../../rtl/ssio_sdr_in.v
VERILOG_SOURCES += ../../rtl/mii_phy_if.v
VERILOG_SOURCES += ../../rtl/eth_mac_1g.v
VERILOG_SOURCES += ../../rtl/axis_gmii_rx.v
VERILOG_SOURCES += ../../rtl/axis_gmii_tx.v
VERILOG_SOURCES += ../../rtl/lfsr.v
VERILOG_SOURCES += ../../lib/axis/rtl/axis_adapter.v
VERILOG_SOURCES += ../../lib/axis/rtl/axis_async_fifo.v
VERILOG_SOURCES += ../../lib/axis/rtl/axis_async_fifo_adapter.v

# module parameters
export PARAM_AXIS_DATA_WIDTH ?= 8
export PARAM_AXIS_KEEP_ENABLE ?= $(shell expr $(PARAM_AXIS_DATA_WIDTH) \> 8 )
export PARAM_AXIS_KEEP_WIDTH ?= $(shell expr $(PARAM_AXIS_DATA_WIDTH) / 8 )
export PARAM_ENABLE_PADDING ?= 1
export PARAM_MIN_FRAME_LENGTH ?= 64
export PARAM_TX_FIFO_DEPTH ?= 16384
export PARAM_TX_FRAME_FIFO ?= 1
export PARAM_TX_DROP_BAD_FRAME ?= $(PARAM_TX_FRAME_FIFO)
export PARAM_TX_DROP_WHEN_FULL ?= 0
export PARAM_RX_FIFO_DEPTH ?= 16384
export PARAM_RX_FRAME_FIFO ?= 1
export PARAM_RX_DROP_BAD_FRAME ?= $(PARAM_RX_FRAME_FIFO)
export PARAM_RX_DROP_WHEN_FULL ?= $(PARAM_RX_FRAME_FIFO)

SIM_BUILD ?= sim_build_$(MODULE)

ifeq ($(SIM), icarus)
	PLUSARGS += -fst

	COMPILE_ARGS += -P $(TOPLEVEL).AXIS_DATA_WIDTH=$(PARAM_AXIS_DATA_WIDTH)
	COMPILE_ARGS += -P $(TOPLEVEL).AXIS_KEEP_ENABLE=$(PARAM_AXIS_KEEP_ENABLE)
	COMPILE_ARGS += -P $(TOPLEVEL).AXIS_KEEP_WIDTH=$(PARAM_AXIS_KEEP_WIDTH)
	COMPILE_ARGS += -P $(TOPLEVEL).ENABLE_PADDING=$(PARAM_ENABLE_PADDING)
	COMPILE_ARGS += -P $(TOPLEVEL).MIN_FRAME_LENGTH=$(PARAM_MIN_FRAME_LENGTH)
	COMPILE_ARGS += -P $(TOPLEVEL).TX_FIFO_DEPTH=$(PARAM_TX_FIFO_DEPTH)
	COMPILE_ARGS += -P $(TOPLEVEL).TX_FRAME_FIFO=$(PARAM_TX_FRAME_FIFO)
	COMPILE_ARGS += -P $(TOPLEVEL).TX_DROP_BAD_FRAME=$(PARAM_TX_DROP_BAD_FRAME)
	COMPILE_ARGS += -P $(TOPLEVEL).TX_DROP_WHEN_FULL=$(PARAM_TX_DROP_WHEN_FULL)
	COMPILE_ARGS += -P $(TOPLEVEL).RX_FIFO_DEPTH=$(PARAM_RX_FIFO_DEPTH)
	COMPILE_ARGS += -P $(TOPLEVEL).RX_FRAME_FIFO=$(PARAM_RX_FRAME_FIFO)
	COMPILE_ARGS += -P $(TOPLEVEL).RX_DROP_BAD_FRAME=$(PARAM_RX_DROP_BAD_FRAME)
	COMPILE_ARGS += -P $(TOPLEVEL).RX_DROP_WHEN_FULL=$(PARAM_RX_DROP_WHEN_FULL)

	ifeq ($(WAVES), 1)
		VERILOG_SOURCES += iverilog_dump.v
		COMPILE_ARGS += -s iverilog_dump
	endif
else ifeq ($(SIM), verilator)
	COMPILE_ARGS += -Wno-SELRANGE -Wno-WIDTH

	COMPILE_ARGS += -GAXIS_DATA_WIDTH=$(PARAM_AXIS_DATA_WIDTH)
	COMPILE_ARGS += -GAXIS_KEEP_ENABLE=$(PARAM_AXIS_KEEP_ENABLE)
	COMPILE_ARGS += -GAXIS_KEEP_WIDTH=$(PARAM_AXIS_KEEP_WIDTH)
	COMPILE_ARGS += -GENABLE_PADDING=$(PARAM_ENABLE_PADDING)
	COMPILE_ARGS += -GMIN_FRAME_LENGTH=$(PARAM_MIN_FRAME_LENGTH)
	COMPILE_ARGS += -GTX_FIFO_DEPTH=$(PARAM_TX_FIFO_DEPTH)
	COMPILE_ARGS += -GTX_FRAME_FIFO=$(PARAM_TX_FRAME_FIFO)
	COMPILE_ARGS += -GTX_DROP_BAD_FRAME=$(PARAM_TX_DROP_BAD_FRAME)
	COMPILE_ARGS += -GTX_DROP_WHEN_FULL=$(PARAM_TX_DROP_WHEN_FULL)
	COMPILE_ARGS += -GRX_FIFO_DEPTH=$(PARAM_RX_FIFO_DEPTH)
	COMPILE_ARGS += -GRX_FRAME_FIFO=$(PARAM_RX_FRAME_FIFO)
	COMPILE_ARGS += -GRX_DROP_BAD_FRAME=$(PARAM_RX_DROP_BAD_FRAME)
	COMPILE_ARGS += -GRX_DROP_WHEN_FULL=$(PARAM_RX_DROP_WHEN_FULL)

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
