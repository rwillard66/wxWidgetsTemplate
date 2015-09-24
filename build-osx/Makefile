####################################
# OSX MAKEFILE
####################################

GCC=gcc
GPP=g++ -stdlib=libstdc++
CFLAGS=-c -Wall
LIBS=
LDFLAGS=
LINC=-L$(WXLOC)/lib/

# The following line contains VPATH's, INCL (include folders),
# and source files so there is a single location
# to change these that will propagate across the different platforms
include ../Makefile.sources

INCL+=-I/Applications/Xcode.app/Contents/Developer/Platforms/MacOSX.platform/Developer/SDKs/MacOSX10.9.sdk/usr/include/c++/4.2.1
	

# The following section describes graphics files that will be converted to
# .XPM format and included in the application for buttons, etc. All .PNG files
# will be converted to .XPM and then concated into a single image.cpp file.
# TO INCLUDE IMAGE IN APPLICTION:
# - Add images.h file and include it where graphics are necessary
# - include an entry for each image to be included:
#   eg.  extern const char* cancel[];
#   NOTE: the graphic name (cancel in above example) must match the basename of
#         the original .PNG file:
#              cancel.png => cancel.xpm => extern const char* cancel[]
FILESPNG := $(shell ls $(RESLOC)/*.png)
FILESXPM := $(FILESPNG:%.png=%.xpm)
SRC += images.cpp

# The following section controls the wxWidgets includes. To use a different
# build of wxWidgets (eg. debug vs. release), change WXLOC to reflect the path
# of your build
WXLOC=/wxWidgets-3.0.2/build-release/
WXC=`$(WXLOC)wx-config --cxxflags`
WXL=`$(WXLOC)wx-config --libs`

# The following section describes the variables for creating a .app distribution
# bundle:
# override the DIST variable to build the .app bundle
DIST=no
COMPANY=SagaTech
BUNDLEPACKAGE=APPL
BUNDLESIGNATURE=???
OUTPUTPATH = ./
BUNDLE=$(OUTPUTPATH)$(TARGET).app
MACICON=$(BUNDLE)/Contents/$(TARGET).icns
MACPKGINFO=$(BUNDLE)/Contents/PkgInfo
MACINFOPLIST=$(BUNDLE)/Contents/Info.plist

################################################################################

OBJ=$(addsuffix .o, $(basename $(SRC)))

ifeq ($(DIST), yes)
all: $(TARGET) $(BUNDLE) $(MACICON) $(MACPKGINFO) $(MACINFOPLIST)
	@echo "==== Cleaning Up ===="
	@rm -f *.o images.cpp *.icns
else
all: $(TARGET)
endif

$(TARGET): $(OBJ)
	$(GPP) $(LINC) $(OBJ) $(LDFLAGS) -g -o $@ $(LIBS) $(WXL)

.cpp.o:
	$(GCC) $< $(INCL) $(WXC) $(CFLAGS) -g -o $@

clean:
	rm -f *.o $(TARGET) $(TARGET).icns images.cpp $(RESLOC)/*.xpm
	rm -rf $(TARGET).app

test:
	@echo $(OBJ)

wxver:
	@echo "Building against: $(WXLOC)"

help:
	@echo " "
	@echo "make <option>"
	@echo "  DIST:"
	@echo "      Adding DIST=yes to make command creates linux distribution package."
	@echo "       File $(TARGET).png must exist in $(RESLOC). This is converted and"
	@echo "       used as the icon file."
	@echo " "
	@echo "  wxver:"
	@echo "      Displays current wxWidgets location to build against."
	@echo " "
	@echo "  install:"
	@echo "      Copies resultant .app bundle to /Applications"
	@echo " "
	
install: 
	@cp -av $(BUNDLE) /Applications/$(BUNDLE)

###############################################################
# OSX application bundle functions
###############################################################


$(BUNDLE):
	@echo "==== Building bundle directory structure ===="
	@mkdir -p $(OUTPUTPATH)
	@mkdir -p $(BUNDLE)/Contents
	@mkdir -p $(BUNDLE)/Contents/MacOS
	@mkdir -p $(BUNDLE)/Contents/Resources
	@cp -f $(TARGET) $(BUNDLE)/Contents/MacOS/$(TARGET)

#  This copies the icon file into the bundle.
$(MACICON): 
	@echo "==== Creating icon and inserting into bundle ===="
	@convert $(RESLOC)/$(TARGET).png $(TARGET).icns
	@cp -f $(TARGET).icns $(BUNDLE)/Contents/Resources/$(TARGET).icns

#  This creates the Contents/PkgInfo file.
$(MACPKGINFO):
	@echo "==== Creating PkgInfo ===="
	@touch $(MACPKGINFO)
	@echo "$(BUNDLEPACKAGE)$(BUNDLESIGNATURE)" > $(MACPKGINFO)

images.cpp: $(FILESPNG)
	@echo "* Generating graphics"
	@$(foreach png,$^,convert $(png) $(basename $(png)).xpm;)
	@touch images.cpp
	@echo '#include "images.h"\n\n' >> images.cpp
	@$(foreach xpm,$^, cat $(basename $(xpm)).xpm >> images.cpp;)
	@sed -i '' 's/static/const/' images.cpp

#  This creates the Contents/Info.plist file.
$(MACINFOPLIST):
	@echo "==== Creating Info.plist ===="
	@touch $(MACINFOPLIST)
	@echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>" >> $(MACINFOPLIST)
	@echo "<!DOCTYPE plist PUBLIC \"-//Apple Computer//DTD PLIST 1.0//EN\" \"http://www.apple.com/DTDs/PropertyList-1.0.dtd\">" >> $(MACINFOPLIST)
	@echo "<plist version=\"1.0\">" >> $(MACINFOPLIST)
	@echo "<dict>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleDevelopmentRegion</key>" >> $(MACINFOPLIST)
	@echo "   <string>English</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleExecutable</key>" >> $(MACINFOPLIST)
	@echo "   <string>$(TARGET)</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleIconFile</key>" >> $(MACINFOPLIST)
	@echo "   <string>$(TARGET).icns</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleName</key>" >> $(MACINFOPLIST)
	@echo "   <string>$(TARGET)</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleIdentifier</key>" >> $(MACINFOPLIST)
	@echo "   <string>com.$(COMPANY).$(TARGET)</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleInfoDictionaryVersion</key>" >> $(MACINFOPLIST)
	@echo "   <string>6.0</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundlePackageType</key>" >> $(MACINFOPLIST)
	@echo "   <string>$(BUNDLEPACKAGE)</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleVersion</key>" >> $(MACINFOPLIST)
	@echo "   <string>$(VER)</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleShortVersionString</key>" >> $(MACINFOPLIST)
	@echo "   <string>$(VER)</string>" >> $(MACINFOPLIST)
	@echo "   <key>CFBundleGetInfoString</key>" >> $(MACINFOPLIST)
	@echo "   <string>$(TARGET), Version $(VER), $(COMPANY)</string>" >> $(MACINFOPLIST)
	@echo "</dict>" >> $(MACINFOPLIST)
	@echo "</plist>" >> $(MACINFOPLIST)


















