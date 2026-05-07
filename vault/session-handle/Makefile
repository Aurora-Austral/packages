# Compiler
AU = austral

# Files
INTERFACES = session.aui
BODIES = session.aum
TEST_BODIES = session.test.aum

# Output
OUTPUT = session_test

all: compile

compile:
	$(AU) compile $(INTERFACES),$(BODIES) --output=$(OUTPUT)

test:
	$(AU) compile $(INTERFACES),$(BODIES) $(TEST_BODIES) --entrypoint=session_test:main --output=$(OUTPUT)
	./$(OUTPUT)

clean:
	rm -f $(OUTPUT)
	rm -f calltree.html
