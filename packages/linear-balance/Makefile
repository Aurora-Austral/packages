# Compiler
AU = austral

# Files
INTERFACES = balance.aui
BODIES = balance.aum
TEST_BODIES = balance.test.aum

# Output
OUTPUT = balance_test

all: compile

compile:
	$(AU) compile $(INTERFACES),$(BODIES) --output=$(OUTPUT)

test:
	$(AU) compile $(INTERFACES),$(BODIES) $(TEST_BODIES) --entrypoint=balance_test:main --output=$(OUTPUT)
	./$(OUTPUT)

clean:
	rm -f $(OUTPUT)
	rm -f calltree.html
