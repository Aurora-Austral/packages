# Compiler
AU = austral

# Files
INTERFACES = secret.aui
BODIES = secret.aum
TEST_BODIES = secret.test.aum

# Output
OUTPUT = secret_test

all: compile

compile:
	$(AU) compile $(INTERFACES),$(BODIES) --output=$(OUTPUT)

test:
	$(AU) compile $(INTERFACES),$(BODIES) $(TEST_BODIES) --entrypoint=secret_test:main --output=$(OUTPUT)
	./$(OUTPUT)

clean:
	rm -f $(OUTPUT)
	rm -f calltree.html
