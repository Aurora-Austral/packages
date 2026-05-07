# Compiler
AU = austral

# Files
INTERFACES = event.aui
BODIES = event.aum
TEST_BODIES = event.test.aum

# Output
OUTPUT = event_test

all: compile

compile:
	$(AU) compile $(INTERFACES),$(BODIES) --output=$(OUTPUT)

test:
	$(AU) compile $(INTERFACES),$(BODIES) $(TEST_BODIES) --entrypoint=event_test:main --output=$(OUTPUT)
	./$(OUTPUT)

clean:
	rm -f $(OUTPUT)
	rm -f calltree.html
