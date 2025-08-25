cmake_minimum_required(VERSION 3.0.2...3.16.3)

option(BUILD_SHARED_LIBS "Create a shared library instead of a static library." ON)

project(skeleton_libretro LANGUAGES C)

# Sources.
add_library(skeleton_libretro
	"libretro.c"
)

############################################
# Standard libretro core boilerplate code. #
############################################

# Avoid some relocation-related linker errors.
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Include libretro-common, so that 'libretro.h' can be found.
target_include_directories(skeleton_libretro PRIVATE "libretro-common/include")

# Provide an 'stdint.h' for older versions of MSVC.
if(MSVC AND MSVC_VERSION LESS 1600)
	target_include_directories(skeleton_libretro PRIVATE "libretro-common/include/compat/msvc")
endif()

# Give the code a way of determining that it is targeting libretro.
target_compile_definitions(skeleton_libretro PRIVATE __LIBRETRO__)

# Adjust the library's filename. This is relied upon by libretro's CI infrastructure.

# Remove the 'lib' prefix.
set_target_properties(skeleton_libretro PROPERTIES PREFIX "")

# Append the given suffix.
set_target_properties(skeleton_libretro PROPERTIES OUTPUT_NAME "skeleton_libretro${LIBRETRO_SUFFIX}")

# Emscripten builds are expected to use the '.bc' file extension instead of '.a'.
if(LIBRETRO_STATIC AND EMSCRIPTEN)
	set_target_properties(skeleton_libretro PROPERTIES SUFFIX ".bc")
endif()
