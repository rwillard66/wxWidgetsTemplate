####################################
# LINUX MAKEFILE
####################################

GCC=gcc
GPP=g++
CFLAGS=-c -Wall
LIBS=
LDFLAGS=

# The following line contains VPATH's, INCL (include folders),
# and source files so there is a single location
# to change these that will propagate across the different platforms
include ../Makefile.sources

LINC=

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

# The following section controls the wxWidgets includes. To use a different
# build of wxWidgets (eg. debug vs. release), change WXLOC to reflect the path
# of your build
WXLOC=/home/robw/tools/wxWidgets-3.0.2/build-debug/
WXC=`$(WXLOC)wx-config --cxxflags`
WXL=`$(WXLOC)wx-config --libs`

FORM=$(RESLOC)/forms.fbp
FORMCLASS=$(SRCLOC)/formclass.cpp

# The following section describes the variables for creating a .deb distribution
# package:
# override the DIST variable to build .deb
DIST=no
DEB=$(TARGET)-$(VER)
# Creator/Maintainer of package
CREATOR=Rob Willard
EMAIL=robw@sagatech.ca
# a short description of the application. This displays as the descriptor for
# the installation package, and must be less than 60 characters
SDESC=Label printer desktop application for Sagatech
# Full description of the package. Lines should be 80 characters or less, and
# each line must start with a space. 
LDESC= \
Desktop interface for Sagatech label printers. Allows for printing of Product,\n \
Location, and Vial labels utilizing the Zebra label printer located at the \n \
shipping desk. Also prints BIN labels utilizing the Zebra printer located at \n \
Rob's Desk.
# indicates any additional dependencies for apt-get. This must be comma separated
DEPS= libwxbase3.0-0, libwxgtk3.0-0
# reason for this release
CHANGEREASON=Initial Release

################################################################################

OBJ=$(addsuffix .o, $(basename $(SRC)))


ifeq ($(DIST), yes)
all: $(TARGET) $(DEB)
else
all: $(TARGET)
endif

$(TARGET): $(FORMCLASS) $(OBJ)
	$(GPP) $(LINC) $(OBJ) $(LDFLAGS) -g -o $@ $(LIBS) $(WXL)

.cpp.o:
	$(GCC) $< $(INCL) $(WXC) $(CFLAGS) -g -o $@

clean:
	rm -f *.o $(TARGET) $(TARGET).ico *.changes *deb
	rm -f $(RESLOC)/*.xpm images.cpp
	rm -rf $(DEB)

test:
	@echo $(OBJ)
	
wxver:
	@echo "Building against: $(WXLOC)"
	
$(FORMCLASS): $(FORM)
	@echo "* Generating Forms"
	wxformbuilder -g $<
	
install:
	cp $(TARGET) /usr/local/bin/

images.cpp: $(FILESPNG)
	@echo " ==> Generating graphics"
	$(foreach png,$^,convert $(png) $(basename $(png)).xpm;)
	@touch images.cpp
	@echo '#include "images.h"\n\n' >> images.cpp
	@$(foreach xpm,$^, cat $(basename $(xpm)).xpm >> images.cpp;)
	sed -i 's/static/const/' images.cpp
	rm -f $(RESLOC)/*.xpm
	
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
	@echo "      Copies resultant program file to /usr/local/bin/"
	@echo " "


#####################################
# This section creates a .deb package 
# for installation
#####################################
$(DEB):
	@echo "===== Building .deb ===== "
	@echo " ===> Creating icon file"
	@convert $(RESLOC)/$(TARGET).png $(TARGET).ico
	@echo " ===> Creating .deb build directory"
	mkdir -p $(DEB)
	cp -f $(TARGET) $(DEB)
	cp -f $(TARGET).ico $(DEB)/$(TARGET).ico
	@echo " ===> Creating .desktop file"
	@touch $(DEB)/$(TARGET).desktop
	@echo "[Desktop Entry]" >> $(DEB)/$(TARGET).desktop
	@echo "Name[en_CA]=$(TARGET)" >> $(DEB)/$(TARGET).desktop
	@echo "Comment=$(SDESC)" >> $(DEB)/$(TARGET).desktop
	@echo "Icon=/usr/share/$(TARGET)/$(TARGET).ico" >> $(DEB)/$(TARGET).desktop
	@echo "Exec=/usr/local/bin/$(TARGET)" >> $(DEB)/$(TARGET).desktop
	@echo "Version=$(VER)" >> $(DEB)/$(TARGET).desktop
	@echo "Type=Application" >> $(DEB)/$(TARGET).desktop
	@echo "Terminal=false" >> $(DEB)/$(TARGET).desktop
	@echo " ===> Archiving files"
	tar -zcvf $(DEB).tar.gz $(DEB)
	cp -f $(DEB).tar.gz $(DEB)
	@echo " ===> Running dh_make"
	@cd $(DEB); \
	yes | dh_make -s -e robw@sagatech.ca -f $(DEB).tar.gz
	rm $(DEB)/debian/*.ex
	rm $(DEB)/debian/*.EX
	rm $(DEB)/debian/README*
	@echo " ===> Creating Control file"
	rm $(DEB)/debian/control
	@touch $(DEB)/debian/control
	@echo "Source: $(TARGET)" >> $(DEB)/debian/control
	@echo "Section: Sagatech" >> $(DEB)/debian/control
	@echo "Priority: optional" >> $(DEB)/debian/control
	@echo "Maintainer: $(CREATOR) <$(EMAIL)>" >> $(DEB)/debian/control
	@echo "Build-Depends: debhelper (>= 8.0.0)" >> $(DEB)/debian/control
	@echo "Standards-Version: 3.9.4" >> $(DEB)/debian/control
	@echo "Homepage: http://www.sagatech.ca" >> $(DEB)/debian/control
	@echo "" >> $(DEB)/debian/control
	@echo "Package: $(TARGET)" >> $(DEB)/debian/control
	@echo "Architecture: any" >> $(DEB)/debian/control
	@echo "Depends: \$${shlibs:Depends}, \$${misc:Depends}, $(DEPS)" >> $(DEB)/debian/control
	@echo "Description: $(SDESC)" >> $(DEB)/debian/control
	@echo " $(LDESC)" >> $(DEB)/debian/control
	@echo " ===> Creating Rules file"
	rm $(DEB)/debian/rules
	@touch $(DEB)/debian/rules
	@echo "#!/usr/bin/make -f" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "ICON=\$$(CURDIR)/$(TARGET).ico" >> $(DEB)/debian/rules
	@echo "SCRIPT=\$$(CURDIR)/$(TARGET)" >> $(DEB)/debian/rules
	@echo "LAUNCHER=\$$(CURDIR)/$(TARGET).desktop" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "DEST1=\$$(CURDIR)/debian/$(TARGET)/usr/share/$(TARGET)" >> $(DEB)/debian/rules
	@echo "DEST2=\$$(CURDIR)/debian/$(TARGET)/usr/share/applications" >> $(DEB)/debian/rules
	@echo "DEST3=\$$(CURDIR)/debian/$(TARGET)/usr/local/bin" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "build: build-stamp" >> $(DEB)/debian/rules
	@echo "build-stamp:" >> $(DEB)/debian/rules
	@echo "	dh_testdir" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "	touch build-stamp" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "clean:" >> $(DEB)/debian/rules
	@echo "	dh_testdir" >> $(DEB)/debian/rules
	@echo "	dh_testroot" >> $(DEB)/debian/rules
	@echo "	rm -f build-stamp" >> $(DEB)/debian/rules
	@echo "	dh_clean" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "install: build clean \$$(ICON) \$$(SCRIPT) \$$(LAUNCHER)" >> $(DEB)/debian/rules
	@echo "	dh_testdir" >> $(DEB)/debian/rules
	@echo "	dh_testroot" >> $(DEB)/debian/rules
	@echo "	dh_prep" >> $(DEB)/debian/rules
	@echo "	dh_installdirs" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "	mkdir -m 755 -p \$$(DEST1)" >> $(DEB)/debian/rules
	@echo "	mkdir -m 755 -p \$$(DEST2)" >> $(DEB)/debian/rules
	@echo "	mkdir -m 755 -p \$$(DEST3)" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "	install -m 666 \$$(ICON) \$$(DEST1)" >> $(DEB)/debian/rules
	@echo "	install -m 777 \$$(SCRIPT) \$$(DEST3)" >> $(DEB)/debian/rules
	@echo "	install -m 777 \$$(LAUNCHER) \$$(DEST2)" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "binary-indep: build install" >> $(DEB)/debian/rules
	@echo "	dh_testdir" >> $(DEB)/debian/rules
	@echo "	dh_testroot" >> $(DEB)/debian/rules
	@echo "	dh_installchangelogs" >> $(DEB)/debian/rules
	@echo "	dh_installdocs" >> $(DEB)/debian/rules
	@echo "	dh_installexamples" >> $(DEB)/debian/rules
	@echo "	dh_installman" >> $(DEB)/debian/rules
	@echo "	dh_link" >> $(DEB)/debian/rules
	@echo "	dh_compress" >> $(DEB)/debian/rules
	@echo "	dh_fixperms" >> $(DEB)/debian/rules
	@echo "	dh_installdeb" >> $(DEB)/debian/rules
	@echo "	dh_gencontrol" >> $(DEB)/debian/rules
	@echo "	dh_md5sums" >> $(DEB)/debian/rules
	@echo "	dh_builddeb" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "binary-arch: build install" >> $(DEB)/debian/rules
	@echo "" >> $(DEB)/debian/rules
	@echo "binary: binary-indep binary-arch" >> $(DEB)/debian/rules
	@echo ".PHONY: build clean binary-indep binary-arch binary install" >> $(DEB)/debian/rules
	@echo " ===> Creating Changelog"
	rm $(DEB)/debian/changelog
	@touch $(DEB)/debian/changelog
	@echo "$(TARGET) ($(VER)) stable; urgency=low" >> $(DEB)/debian/changelog
	@echo "" >> $(DEB)/debian/changelog
	@echo "  * $(CHANGEREASON)" >> $(DEB)/debian/changelog
	@echo "" >> $(DEB)/debian/changelog
	@echo " -- $(CREATOR) <$(EMAIL)>  $(shell  date +'%a, %d %b %Y %T %z')" >> $(DEB)/debian/changelog
	@echo " ===> Creating final files"
	cd $(DEB); fakeroot dpkg-buildpackage -b
	@echo " ===> Cleaning up"
	rm -rf $(DEB)
	rm -f *.tar.gz *.o *.ico images.cpp
	@echo "=== DEB COMPLETE ==="
	
	
