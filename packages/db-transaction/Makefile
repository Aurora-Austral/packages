# Detect compiler - excluding broken Nix binaries in .local/bin
AU := $(shell \
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

ifeq ($(AU),)
    $(error Austral compiler not found. Please install austral in /usr/local/bin)
endif

# Files
INTERFACES = db.aui
BODIES = db.aum
TEST_BODIES = db.test.aum

# Output
OUTPUT = db_test

all: compile

compile:
	@echo "Building DB Transaction library..."
	@echo "Using compiler: $(AU)"
	$(AU) compile $(INTERFACES),$(BODIES) --no-entrypoint --target-type=c --output=$(OUTPUT)
	@echo "Build successful!"

test:
	@echo "Running tests..."
	@echo "Using compiler: $(AU)"
	$(AU) compile $(INTERFACES),$(BODIES) $(TEST_BODIES) --entrypoint=db_test:main --output=$(OUTPUT)
	./$(OUTPUT)

clean:
	rm -f $(OUTPUT) calltree.html error.html

.PHONY: all compile test clean
