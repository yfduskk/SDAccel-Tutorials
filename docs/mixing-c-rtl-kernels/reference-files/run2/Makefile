#######################################################################################
.PHONY: help
help:
	@echo "Makefile Usage:"
	@echo "  make all TARGET=<sw_emu/hw_emu/hw> DEVICE=<FPGA platform>"
	@echo "      Command to generate the design for specified Target and Device."
	@echo ""
	@echo "  make exe "
	@echo "      Command to generate host."
	@echo ""
	@echo "  make xclbin "
	@echo "      Command to generate hardware platform files(xo,xclbin)."
	@echo ""
	@echo "  make clean "
	@echo "      Command to remove the generated files."
	@echo ""
#######################################################################################
TARGET := sw_emu
DEVICE := xilinx_u200_xdma_201830_1
EXECUTABLE := host
XO := krnl_vadd.$(TARGET).$(DEVICE).xo
XCLBIN := krnl_vadd.$(TARGET).$(DEVICE).xclbin

# Host building global settings
CXXFLAGS := -I$(XILINX_XRT)/include/ -I$(XILINX_VIVADO)/include/ -Wall -O0 -g -std=c++14 -L$(XILINX_XRT)/lib/ -lOpenCL -lpthread -lrt -lstdc++ 

# Kernel compiler & linker global settings
CLFLAGS := -t $(TARGET) --platform $(DEVICE) -g
LDCLFLAGS := -t $(TARGET) --platform $(DEVICE) -g

all: $(EXECUTABLE) $(XO) $(XCLBIN) emconfig

exe: $(EXECUTABLE)

xclbin: $(XO) $(XCLBIN)

# Building kernel
$(XO): ../src/kernel_cpp/krnl_vadd.cpp
	xocc $(CLFLAGS) -c -k krnl_vadd -I'$(<D)' -o'$@' '$<'
$(XCLBIN): krnl_vadd.$(TARGET).$(DEVICE).xo ../../workspace/rtl_project/src/sdx_rtl_kernel/sdx_kernel_wizard_0/sdx_kernel_wizard_0.xo
	xocc $(LDCLFLAGS) -l --nk krnl_vadd:1:krnl_vadd_1 --nk sdx_kernel_wizard_0:1:sdx_kernel_wizard_0_1 -o'$@' $(+)

# Building Host
$(EXECUTABLE): ../src/host/host_step2.cpp
	xcpp $(CXXFLAGS) -o '$@' '$<'

.PHONY: emconfig
emconfig: 
	emconfigutil --platform $(DEVICE)

# Cleaning stuff
.PHONY: clean
clean:
	rm -f $(EXECUTABLE) *krnl_vadd.$(TARGET).$(DEVICE).* *.log *.json *.xo
