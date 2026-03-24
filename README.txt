## Changing test by edit "Makefile" file:
	vi Makefile
	TB_NAME ?= // add test name here

## How to use makefile in this environment:
		
	-initiate project:
        make init
	-compile all sources and testbenches:
		make compile
	-simulation:
		make simulation
	-clean all outputs:
		make clean
    -running all:
        make all