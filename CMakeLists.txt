cmake_minimum_required(VERSION 3.0.2...3.16.3)

option(BUILD_SHARED_LIBS "Create a shared library instead of a static library." ON)

project(skeleton_libretro LANGUAGES C)

# Sources.
add_library(skeleton_libretro
	"libretro.c"
)

# Avoid some linker errors to do with relocations.
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Include libretro-common, so that 'libretro.h' can be found.
target_include_directories(skeleton_libretro PRIVATE "libretro-common/include")

# Provide an 'stdint.h' for older versions of MSVC.
if(MSVC AND MSVC_VERSION LESS 1600)
	target_include_directories(skeleton_libretro PRIVATE "libretro-common/include/compat/msvc")
endif()

# Prevent the 'lib' prefix from being added, to match the behaviour of the Makefile.
set_target_properties(skeleton_libretro PROPERTIES PREFIX "")

# Let the software know that it is targeting libretro.
target_compile_definitions(skeleton_libretro PRIVATE __LIBRETRO__)
