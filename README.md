# mulle-xcode-to-cmake

A little tool to convert [Xcode](https://developer.apple.com/xcode/) projects
to [cmake](https://cmake.org/) CMakeLists.txt.

You can specify the target to export. If you don't specify a target, all
targets are exported.
It doesn't do a perfect job, but it's better than doing it all by hand.
It can convert most targets, but it will do better with libraries and tools or
frameworks.

> This is useful for "one-shot" conversions. For continous conversions from
> Xcode to cmake, the author prefers to use
> [mulle-match](https://github.com/mulle-sde/mulle-match) together with
> [mulle-match-to-cmake](https://github.com/mulle-sde/mulle-match/blob/release/mulle-match-to-cmake)
> nowadays.

Fork      |  Build Status | Master Version
----------|---------------|-----------------------------------
[Mulle kybernetiK](//github.com/mulle-nat/mulle-xcode-to-cmake) | [![Build Status](https://travis-ci.org/mulle-nat/mulle-xcode-to-cmake.svg?branch=master)](https://travis-ci.org/mulle-nat/mulle-xcode-to-cmake) | ![Mulle kybernetiK tag](https://img.shields.io/github/tag/mulle-nat/mulle-xcode-to-cmake.svg) [![Build Status](https://travis-ci.org/mulle-nat/mulle-xcode-to-cmake.svg?branch=master)](https://travis-ci.org/mulle-nat/mulle-xcode-to-cmake)


## Install

Use the [homebrew](//brew.sh) package manager to install it, or build
it yourself with Xcode:

```
brew install mulle-kybernetik/software/mulle-xcode-to-cmake
```

It is also possible to build using **cmake** and the "CMakeLists.txt" file
generated by mulle-xcode-to-cmake itself. After checking out the sources,
change into the source directory and then

```
mkdir build && cd build
cmake -Wno-dev ..
make
sudo make install
```

On Mac this should work without installing any additional dependencies. On other
platforms it is suggested to install a recent clang version (5 or later) and you
will need libobjc and the gnustep-base developer package, both from GNUstep (or another
implementation of the ObjC runtime and the Foundation library).

On Windows, it is a bit tricky : please refer to this little guide [here](https://github.com/ElMostafaIdrassi/mulle-xcode-to-cmake/blob/release/BUILD-GNUSTEP-WINDOWS.md)


## Usage

```
usage: mulle-xcode-to-cmake [options] <commands> <file.xcodeproj>

Options:
   -2          : CMakeLists.txt includes CMakeSourcesAndHeaders.txt
   -a          : always prefix cmake variables with target
   -b          : suppress boilerplate definitions
   -d          : create static and shared library
   -f          : suppress Foundation (implicitly added)
   -i          : print global include_directories
   -l <lang>   : specify project language (c,c++,objc) (default: objc)
   -n          : suppress find_library trace
   -p          : suppress project
   -P <prefix> : prefix filepaths
   -r          : suppress reminder, what generated this file
   -s <suffix> : create standalone test library (framework/shared)
   -t <target> : target to export
   -u          : add UIKIt
   -w <name>   : add weight to name for sorting

Commands:
   export      : export CMakeLists.txt to stdout
   list        : list targets
   sexport     : export CMakeSourcesAndHeaders.txt to stdout

Environment:
   VERBOSE     : dump some info to stderr
```

### Examples

List all project targets:

```console
$ mulle-xcode-to-cmake list mulle-xcode-to-cmake.xcodeproj
mulle-xcode-to-cmake
mullepbx
```

Create "CMakeLists.txt" for target `mullepbx` leaving out some
boilerplate template code. Invoking `mulle-xcode-to-cmake -b export mulle-xcode-to-cmake.xcodeproj` yields:

```console
# Generated by mulle-xcode-to-cmake [2017-2-21 18:16:4]

project( mulle-xcode-to-cmake)

cmake_minimum_required (VERSION 3.4)


##
## mulle-xcode-to-cmake Files
##

set( MULLE_XCODE_TO_CMAKE_SOURCES
src/mulle-xcode-to-cmake/NSArray+Path.m
src/mulle-xcode-to-cmake/NSString+ExternalName.m
src/mulle-xcode-to-cmake/PBXHeadersBuildPhase+Export.m
src/mulle-xcode-to-cmake/PBXPathObject+HierarchyAndPaths.m
src/mulle-xcode-to-cmake/main.m
)

find_library( MULLEPBX_LIBRARY mullepbx)
message( STATUS "MULLEPBX_LIBRARY is ${MULLEPBX_LIBRARY}")

set( MULLE_XCODE_TO_CMAKE_STATIC_DEPENDENCIES
${MULLEPBX_LIBRARY}
)

find_library( FOUNDATION_LIBRARY Foundation)
message( STATUS "FOUNDATION_LIBRARY is ${FOUNDATION_LIBRARY}")

set( MULLE_XCODE_TO_CMAKE_DEPENDENCIES
${FOUNDATION_LIBRARY}
)


##
## mullepbx Files
##

set( MULLEPBX_PUBLIC_HEADERS
src/PBXReading/MullePBXUnarchiver.h
src/PBXReading/PBXObject.h
src/PBXWriting/MullePBXArchiver.h
src/PBXWriting/PBXObject+PBXEncoding.h
)

set( MULLEPBX_PROJECT_HEADERS
)

set( MULLEPBX_PRIVATE_HEADERS
src/PBXReading/NSObject+DecodeWithObjectStorage.h
src/PBXReading/NSString+KeyFromSetterSelector.h
src/PBXReading/NSString+LeadingDotExpansion.h
src/PBXReading/PBXProjectProxy.h
src/PBXWriting/MulleSortedKeyDictionary.h
)

set( MULLEPBX_SOURCES
src/PBXReading/MullePBXUnarchiver.m
src/PBXReading/NSObject+DecodeWithObjectStorage.m
src/PBXReading/NSString+KeyFromSetterSelector.m
src/PBXReading/NSString+LeadingDotExpansion.m
src/PBXReading/PBXObject.m
src/PBXReading/PBXProjectProxy.m
src/PBXWriting/MullePBXArchiver.m
src/PBXWriting/MulleSortedKeyDictionary.m
src/PBXWriting/PBXObject+PBXEncoding.m
)


##
## mulle-xcode-to-cmake
##

add_executable( mulle-xcode-to-cmake MACOSX_BUNDLE
${MULLE_XCODE_TO_CMAKE_SOURCES}
${MULLE_XCODE_TO_CMAKE_PUBLIC_HEADERS}
${MULLE_XCODE_TO_CMAKE_PROJECT_HEADERS}
${MULLE_XCODE_TO_CMAKE_PRIVATE_HEADERS}
${MULLE_XCODE_TO_CMAKE_RESOURCES}
)

target_include_directories( mulle-xcode-to-cmake
   PUBLIC
      src/PBXReading
      src/PBXWriting
)

add_dependencies( mulle-xcode-to-cmake mullepbx)

target_link_libraries( mulle-xcode-to-cmake
${MULLE_XCODE_TO_CMAKE_STATIC_DEPENDENCIES}
${MULLE_XCODE_TO_CMAKE_DEPENDENCIES}
)


##
## mullepbx
##

add_library( mullepbx STATIC
${MULLEPBX_SOURCES}
${MULLEPBX_PUBLIC_HEADERS}
${MULLEPBX_PROJECT_HEADERS}
${MULLEPBX_PRIVATE_HEADERS}
${MULLEPBX_RESOURCES}
)

target_include_directories( mullepbx
   PUBLIC
      src/PBXReading
      src/PBXWriting
)

install( TARGETS mullepbx DESTINATION "lib")
install( FILES ${MULLEPBX_PUBLIC_HEADERS} DESTINATION "include/mullepbx")
```

You have your own `CMakeLists.txt` template and just want to `include()`
the list of sources as they change in the Xcode project:

`mulle-xcode-to-cmake sexport mulle-xcode-to-cmake.xcodeproj` yields:

```console
##
## mulle-xcode-to-cmake Files
##

set( MULLE_XCODE_TO_CMAKE_SOURCES
src/mulle-xcode-to-cmake/NSArray+Path.m
src/mulle-xcode-to-cmake/NSString+ExternalName.m
src/mulle-xcode-to-cmake/PBXHeadersBuildPhase+Export.m
src/mulle-xcode-to-cmake/PBXPathObject+HierarchyAndPaths.m
src/mulle-xcode-to-cmake/main.m
)


##
## mullepbx Files
##

set( MULLEPBX_PUBLIC_HEADERS
src/PBXReading/MullePBXUnarchiver.h
src/PBXReading/PBXObject.h
src/PBXWriting/MullePBXArchiver.h
src/PBXWriting/PBXObject+PBXEncoding.h
)

set( MULLEPBX_SOURCES
src/PBXReading/MullePBXUnarchiver.m
src/PBXReading/NSObject+DecodeWithObjectStorage.m
src/PBXReading/NSString+KeyFromSetterSelector.m
src/PBXReading/NSString+LeadingDotExpansion.m
src/PBXReading/PBXObject.m
src/PBXReading/PBXProjectProxy.m
src/PBXWriting/MullePBXArchiver.m
src/PBXWriting/MulleSortedKeyDictionary.m
src/PBXWriting/PBXObject+PBXEncoding.m
)
```

### History

This is basically a stripped down version of `mulle_xcode_utility`.

See the [RELEASENOTES.md](RELEASENOTES.md) for what has changed.



### Author

Coded by Nat!


### Contributors

* [@RJVB](https://github.com/RJVB) GNUstep
* [@ElMostafaIdrassi](https://github.com/ElMostafaIdrassi) GNUstep WIN
* [@saxbophone](https://github.com/saxbophone) Bugreports

