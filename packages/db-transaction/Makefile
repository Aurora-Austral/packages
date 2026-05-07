# Compiler
AU = austral

# Files
INTERFACES = db.aui
BODIES = db.aum
TEST_BODIES = db.test.aum

# Output
OUTPUT = db_test

all: compile

compile:
	$(AU) compile $(INTERFACES),$(BODIES) --output=$(OUTPUT)

test:
	$(AU) compile $(INTERFACES),$(BODIES) $(TEST_BODIES) --entrypoint=db_test:main --output=$(OUTPUT)
	./$(OUTPUT)

clean:
	rm -f $(OUTPUT)
	rm -f calltree.html
