# Set  the minimum  required version  of cmake  for a  project.
cmake_minimum_required(VERSION 2.6)

# Add an executable to the project using the specified source files.
add_executable(tetgen tetgen.cxx predicates.cxx)

#Add a library to the project using the specified source files. 
# In Linux/Unix, it will creates the libtet.a
add_library(tet STATIC tetgen.cxx predicates.cxx)

#Set properties on a target. 
#We use this here to set -DTETLIBRARY for when compiling the
#library
set_target_properties(tet PROPERTIES "COMPILE_DEFINITIONS" TETLIBRARY)

# Custom additions for shippable builds that are NOT part of the original TetGen repository.
if ((UNIX AND NOT APPLE) OR (MSYS OR MINGW OR (CMAKE_CXX_COMPILER_ID STREQUAL "GNU")))
    target_link_options(tetgen PRIVATE -static-libgcc -static-libstdc++)
endif()
if (UNIX AND NOT APPLE)
    target_link_options(tetgen PRIVATE "-Wl,--version-script=${CMAKE_CURRENT_SOURCE_DIR}/CMake/symbols_linux_gcc.map")
endif()
if (MSVC)
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()
