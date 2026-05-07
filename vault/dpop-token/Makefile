AU_COMPILER = austral

all: build

build:
	$(AU_COMPILER) compile dpop.aui dpop.aum --output dpop.o

test:
	$(AU_COMPILER) compile dpop.aui,dpop.aum dpop.test.aum --entrypoint=dpop_test:main --output=dpop_test
	./dpop_test

test-all: test

clean:
	rm -f *.o dpop_test
