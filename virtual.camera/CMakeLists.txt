cmake_minimum_required(VERSION 3.5)
project(virtualcam-module)

if(CMAKE_SIZEOF_VOID_P EQUAL 8)
	set(_output_suffix "64")
else()
	set(_output_suffix "32")
endif()

configure_file(
	"${CMAKE_CURRENT_SOURCE_DIR}/module.def.in"
	"${CMAKE_CURRENT_BINARY_DIR}/module.def")

set(MODULE_DESCRIPTION "Virtual Camera output module")

include_directories(${CMAKE_CURRENT_BINARY_DIR}/..)

set(libdshowcapture_SOURCES
	libdshowcapture/source/log.cpp
	libdshowcapture/source/dshow-base.cpp
	libdshowcapture/source/dshow-enum.cpp
	libdshowcapture/source/dshow-formats.cpp
	libdshowcapture/source/dshow-media-type.cpp
	libdshowcapture/source/output-filter.cpp)

set(libdshowcapture_HEADERS
	libdshowcapture/source/ComPtr.hpp
	libdshowcapture/source/CoTaskMemPtr.hpp
	libdshowcapture/source/log.hpp
	libdshowcapture/source/dshow-base.hpp
	libdshowcapture/source/dshow-enum.hpp
	libdshowcapture/source/dshow-formats.hpp
	libdshowcapture/source/dshow-media-type.hpp
	libdshowcapture/source/output-filter.hpp
	libdshowcapture/dshowcapture.hpp)

set(virtualcam-module_SOURCES
	"${CMAKE_CURRENT_BINARY_DIR}/module.def"
	sleepto.cpp
	placeholder.cpp
	module.cpp
	filter.cpp
	shared_memory_queue.cpp
	nv12_scale.cpp)

set(virtualcam-module_HEADERS
	sleepto.h
	filter.hpp
	shared_memory_queue.h
	nv12_scale.h)

if(MSVC)
	add_compile_options("$<IF:$<CONFIG:Debug>,/MTd,/MT>")
endif()

source_group("libdshowcapture\\Source Files" FILES ${libdshowcapture_SOURCES})
source_group("libdshowcapture\\Header Files" FILES ${libdshowcapture_HEADERS})

set(CMAKE_MODULE_LINKER_FLAGS "${MAKE_MODULE_LINKER_FLAGS} /ignore:4104")

add_library(virtualcam-module MODULE
	${libdshowcapture_SOURCES}
	${libdshowcapture_HEADERS}
	${virtualcam-module_SOURCES}
	${virtualcam-module_HEADERS})
target_link_libraries(virtualcam-module
	setupapi
	winmm
	strmiids
	gdiplus)
set_target_properties(virtualcam-module PROPERTIES
		OUTPUT_NAME "virtualcam-module${_output_suffix}")
