CMAKE_MINIMUM_REQUIRED(VERSION 2.6)

#README
# Compile only daisy as a library.
# do not compile the binary because third party libraries are required
# such as png zlib and jpeg

project(daisy)

IF (UNIX OR APPLE)
  check_symbol_exists(mmap64 "sys/mman.h" HAVE_MMAP64)
ENDIF ()

if(${HAVE_MMAP64})
    message("System has large file support.")
    add_definitions(-DHAVE_LARGE_FILE_SUPPORT)
else()
    message("System hasn't large file support.")
endif()

#Detect OpenMP
FIND_PACKAGE(OpenMP) 
if (OPENMP_FOUND) 
  SET(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
  option(USE_OPENMP "Use OpenMP for parallelization" ON)
  add_definitions(-DUSE_OPENMP)
else(OPENMP_FOUND)
  option(USE_OPENMP "Use OpenMP for parallelization" OFF)
endif (OPENMP_FOUND)

#add jpeg support
add_definitions(-DWITH_JPEG)

include_directories(include)

# Make the headers appear in IDEs.
file(GLOB daisy_hdrs include/kutility/*.h include/daisy/*.h)

set(daisy_srcs
    src/daisy.cpp
    src/corecv.cpp
    src/image_io_bmp.cpp
    src/image_io_pnm.cpp
    src/image_io_png.cpp
    src/image_io_jpeg.cpp
    src/image_manipulation.cpp
    src/progress_bar.cpp
    src/general.cpp
    src/interaction.cpp
)

add_library(daisy ${daisy_srcs} ${daisy_hdrs})

ADD_EXECUTABLE(main_daisy src/main.cpp)
TARGET_LINK_LIBRARIES(main_daisy daisy)

