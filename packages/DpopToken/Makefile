# Detect compiler - excluding broken Nix binaries in .local/bin
AU_COMPILER := $(shell \
	if [ -x ".aurora/bin/austral" ]; then \
		echo ".aurora/bin/austral"; \
	else \
		for path in $$(echo $$PATH | tr ':' ' '); do \
			if [ -x "$$path/austral" ] && echo "$$path" | grep -qv "/.local/bin"; then \
				echo "$$path/austral"; \
				break; \
			fi; \
		done; \
	fi)

ifeq ($(AU_COMPILER),)
    $(error Austral compiler not found. Please install austral in /usr/local/bin)
endif

all: build

build:
	@echo "Building DPoP Token library..."
	@echo "Using compiler: $(AU_COMPILER)"
	$(AU_COMPILER) compile dpop.aui,dpop.aum --no-entrypoint --target-type=c --output=dpop.o
	@echo "Build successful!"

test:
	@echo "Running tests..."
	@echo "Using compiler: $(AU_COMPILER)"
	$(AU_COMPILER) compile dpop.aui,dpop.aum dpop.test.aum --entrypoint=dpop_test:main --output=dpop_test
	./dpop_test

test-all: test

clean:
	rm -f *.o dpop_test calltree.html error.html

.PHONY: all build test test-all clean
