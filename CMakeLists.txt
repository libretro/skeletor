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

# Avoid some relocation-related linker errors when building a shared library that depends on static libraries.
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Include libretro-common, so that 'libretro.h' can be found.
target_include_directories(skeleton_libretro PRIVATE "libretro-common/include")

# Provide an 'stdint.h' for older versions of MSVC.
if(MSVC AND MSVC_VERSION LESS 1600)
	target_include_directories(skeleton_libretro PRIVATE "libretro-common/include/compat/msvc")
endif()

# Give the code a way of determining that it is targeting libretro.
target_compile_definitions(skeleton_libretro PRIVATE __LIBRETRO__)

# Allow only the libretro API functions to be exported.
set_target_properties(skeleton_libretro PROPERTIES C_VISIBILITY_PRESET hidden CXX_VISIBILITY_PRESET hidden VISIBILITY_INLINES_HIDDEN ON)

# Adjust the library's filename. This is relied upon by libretro's CI infrastructure.

# Remove the 'lib' prefix.
set_target_properties(skeleton_libretro PROPERTIES PREFIX "")

# Append the given suffix.
set_target_properties(skeleton_libretro PROPERTIES OUTPUT_NAME "skeleton_libretro${LIBRETRO_SUFFIX}")

# Emscripten builds are expected to use the '.bc' file extension instead of '.a'.
if(LIBRETRO_STATIC AND EMSCRIPTEN)
	set_target_properties(skeleton_libretro PROPERTIES SUFFIX ".bc")
endif()

# Obtain the Git revision so that it can be displayed in the core's version string.
find_package(Git)
if(GIT_FOUND)
	execute_process(
		COMMAND ${GIT_EXECUTABLE} rev-parse --short HEAD
		OUTPUT_VARIABLE GIT_VERSION
		OUTPUT_STRIP_TRAILING_WHITESPACE
	)
	if(GIT_VERSION)
		target_compile_definitions(skeleton_libretro PRIVATE GIT_VERSION=" ${GIT_VERSION}")
	endif()
endif()
