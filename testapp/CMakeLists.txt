# Min. CMake version.
cmake_minimum_required(VERSION 2.8.10)

# Project name.
set(NAME testapp)

# A name of the project.
project(${NAME})

# GLUT.
find_package(GLUT)

# Set general compiler flags.
if (WIN32)
	add_definitions(-D__WINDOWS__ -D_CRT_SECURE_NO_WARNINGS -D_CRT_SECURE_NO_DEPRECATE)
endif (WIN32)

if (APPLE)
	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -framework OpenGL -framework GLUT")
endif (APPLE)

# Use GUI.
add_definitions(-DUSE_GUI=1)

# SGL headers and cpps.
file(GLOB SGL_HDR "sgl/*.h")
file(GLOB SGL_SRC "sgl/*.cpp")

set(HDR
	hdrloader.h
	nffread.h
	nffstore.h
	nffwrite.h
	timer.h
	${SGL_HDR}
)

set(SRC
	hdrloader.cpp
	nffread.cpp
	testapp.cpp
	timer.cpp
	${SGL_SRC}
)

# Find includes in corresponding build directories.
set(CMAKE_INCLUDE_CURRENT_DIR ON)

source_group("Header Files\\" FILES ${HDR})
source_group("Source Files\\" FILES ${SRC})

# Add all files to excutable.
add_executable(${NAME}
  ${HDR}
  ${SRC}
)

# Use freeglut on Windows.
if (WIN32)
	if (NOT GLUT_FOUND)
		if (CMAKE_SIZEOF_VOID_P EQUAL 8)
		set(GLUT_LIB_PATH freeglut/lib/x64)
	else(CMAKE_SIZEOF_VOID_P EQUAL 8)
		set(GLUT_LIB_PATH freeglut/lib)
	endif(CMAKE_SIZEOF_VOID_P EQUAL 8)
	find_library(GLUT_LIBRARIES
		NAMES freeglut
		PATHS
		${GLUT_LIB_PATH}
		DOC "The GLUT library"
	)
	find_path(GLUT_INCLUDE_DIR GL/glut.h
		freeglut/include
		DOC "The directory where GL/glut.h resides"
	)
	endif (NOT GLUT_FOUND)
endif (WIN32)

# Add include directories.
set(SGL_INCLUDE_DIR ./sgl)
include_directories (SYSTEM 
	${SGL_INCLUDE_DIR}
	${GLUT_INCLUDE_DIR}
)

# Link libraries.
target_link_libraries (${NAME} ${GLUT_LIBRARIES})

# Set output directory.
set(BIN_DIR ${CMAKE_SOURCE_DIR})

# Properties.
set_target_properties(${NAME} PROPERTIES 
	DEBUG_OUTPUT_NAME ${NAME}_d 
	RELEASE_OUTPUT_NAME ${NAME}	
    RUNTIME_OUTPUT_DIRECTORY ${BIN_DIR}
	RUNTIME_OUTPUT_DIRECTORY_RELEASE ${BIN_DIR}
	RUNTIME_OUTPUT_DIRECTORY_DEBUG ${BIN_DIR}
)

