This directory contains a template for creating wxWidgets desktop applications for Linux, OSX, and Windows.

The directory structure is as follows:

(wxWidgets_application_template) --
                                  | Makefile.sources
                                  | README.txt
                                  |
                                  |-- (src)
                                  |       | main.cpp
                                  |       | images.h
                                  |
                                  |-- (res)
                                  |       | forms.fbp
                                  |
                                  |-- (build-linux)
                                  |       | Makefile
                                  |
                                  |-- (build-osx)
                                  |       | Makefile
                                  |
                                  |-- (build-win)
                                  |       | Makefile
                                  |
                                  |-- (nbproject)
                                          | <<nbproject files>>


forms.fbp:			Blank wxFormBuilder document with paths entered.
Makefile.sources:	This file includes several variables for source inclusion. They are included
					in the main Makefile for each operating system, so all changes will propagate
					throughout the platforms.
REAME.txt:			This file.
main.cpp:			Template for main. Includes instructions for adding wx Form class.
images.h:			Prototype file for including images in the application. Full directions for use
					are included at the top of the file.
Makefile(x3):		Platform specific make instructions. Each variable is documented.


Distribution
============
Each makefile contains a variable DIST that is set to 'no'. Overridding this to 'yes' builds 
the application, then bundles it for distribution on the respective platform.

Linux - A .deb and .changes file are created that can be added to the repository
OSX - A .app bundle is created. This can then be added to a .dmg distribution by following 
      the instructions in the Sagatech wiki.
Windows - ((UNIMPLEMENTED)) Creates the INNO script file for creating a distribution executable.
