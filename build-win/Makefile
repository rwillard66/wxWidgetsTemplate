####################################
# MS WINDOWS MAKEFILE
####################################

GCC=gcc
GPP=g++
CFLAGS=-c -Wall
LIBS=
LDFLAGS=
LINC=
WINTARGET=$(TARGET).exe

# The following line contains VPATH's, INCL (include folders),
# and source files so there is a single location
# to change these that will propagate across the different platforms
include ../Makefile.sources

SRC+=images.cpp

OBJ=$(addsuffix .o, $(basename $(SRC)))

WXLOC=c:/wxWidgets-3.0.2/build-release/
WXC=`$(WXLOC)wx-config --cxxflags`
WXL=`$(WXLOC)wx-config --libs`

all: $(WINTARGET)

$(WINTARGET): $(OBJ)
	$(GPP) $(LINC) $(OBJ) $(LDFLAGS) -g -o $@ $(LIBS) $(WXL)

.cpp.o:
	$(GCC) $< $(INCL) $(WXC) $(CFLAGS) -g -o $@

clean:
	@rm -f *.o $(WINTARGET)

test:
	@echo $(OBJ)

wxver:
	@echo $(WXLOC)

images.cpp: $(FILESPNG)
	@echo "* Generating graphics"
	@$(foreach png,$^,convert $(png) $(basename $(png)).xpm;)
	@touch images.cpp
	@echo '#include "images.h"\n\n' >> images.cpp
	@$(foreach xpm,$^, cat $(basename $(xpm)).xpm >> images.cpp;)
	@sed -i '' 's/static/const/' images.cpp

