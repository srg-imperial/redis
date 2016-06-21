SH     = $(shell which sh)
PWD    = $(shell pwd)

# Varan
VX     = # PATH TO VARAN

# Compilers
GCC    = $(shell which gcc)
CLANG  = $(shell which clang)

# For debugging
#REDIS_CFG = OPTIMIZATION=-O0 DEBUG=-g

# Where each version expects to be installed
INSTALL_DIR       = $(PWD)/install
INSTALL_BUILD_DIR = $(INSTALL_DIR)/redis

# Where different versions are installed
INSTALL_GCC        = $(PWD)/install/gcc-system
INSTALL_GCC_ASAN   = $(PWD)/install/gcc-asan-system
INSTALL_CLANG      = $(PWD)/install/clang-system
INSTALL_CLANG_ASAN = $(PWD)/install/clang-asan-system

# Binaries inside each build directory
REDIS_GCC        = $(INSTALL_GCC)/bin/redis-server
REDIS_GCC_ASAN   = $(INSTALL_GCC_ASAN)/bin/redis-server
REDIS_CLANG      = $(INSTALL_CLANG)/bin/redis-server
REDIS_CLANG_ASAN = $(INSTALL_CLANG_ASAN)/bin/redis-server

# Script to run REDIS with Varan
REDIS_DIR = $(PWD)/bin
REDIS = $(REDIS_DIR)/redis

# Command lines to run Redis with Varan inside script
RUN_GCC_GCC          =                              $(VX) $(REDIS_GCC)   $(REDIS_GCC)        -- \$$@
RUN_GCC_GCC_ASAN     = ASAN_OPTIONS="handle_segv=0" $(VX) $(REDIS_GCC)   $(REDIS_GCC_ASAN)   -- \$$@
RUN_CLANG_CLANG      =                              $(VX) $(REDIS_CLANG) $(REDIS_CLANG)      -- \$$@
RUN_CLANG_CLANG_ASAN = ASAN_OPTIONS="handle_segv=0" $(VX) $(REDIS_CLANG) $(REDIS_CLANG_ASAN) -- \$$@

# Main target
all: vx-gcc-gcc_asan

# ==============================================================================
# GCC targets
# ==============================================================================

# Set up script for native leader and native follower built with gcc
vx-gcc-gcc: $(REDIS_GCC)
	mkdir -p $(REDIS_DIR) || true
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm $(REDIS) || true
	echo "#!$(SH)"        >  $(REDIS)
	echo "$(RUN_GCC_GCC)" >> $(REDIS)
	chmod a+x $(REDIS)

# Set up script for native leader and sanitized follower built with gcc
vx-gcc-gcc_asan: $(REDIS_GCC) $(REDIS_GCC_ASAN)
	mkdir -p $(REDIS_DIR) || true
	rm -rf $(INSTALL_BUILD_DIR) || true
	cp -r $(INSTALL_GCC) $(INSTALL_BUILD_DIR)
	rm $(REDIS) || true
	echo "#!$(SH)"             >  $(REDIS)
	echo "$(RUN_GCC_GCC_ASAN)" >> $(REDIS)
	chmod a+x $(REDIS)

# Build using gcc and no sanitization
$(REDIS_GCC):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean || true
	rm -rf ../.make-settings || true
	make -C ..\
	   	PREFIX=$(INSTALL_BUILD_DIR)\
		CC="$(GCC)"\
		V=1\
		$(REDIS_CFG)\
	   	install
	rm -rf $(INSTALL_GCC) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC)

# Build using gcc and sanitization
$(REDIS_GCC_ASAN):
	rm -rf $(INSTALL_BUILD_DIR) || true
	make -C .. clean
	rm -rf ../.make-settings || true
	make -C .. \
	   	PREFIX=$(INSTALL_BUILD_DIR) \
		CC="$(GCC)" \
		REDIS_CFLAGS="-fsanitize=address" \
		REDIS_LDFLAGS="-fsanitize=address" \
		V=1 \
		$(REDIS_CFG) \
	   	install
	rm -rf $(INSTALL_GCC_ASAN) || true
	mv $(INSTALL_BUILD_DIR) $(INSTALL_GCC_ASAN)

# ==============================================================================
# misc targets
# ==============================================================================

clean:
	make -C .. distclean || true
	rm -rf $(INSTALL_DIR) || true
	rm -rf $(REDIS_DIR) || true
