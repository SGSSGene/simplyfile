# SPDX-FileCopyrightText: 2018 Lutz Freitag
# SPDX-License-Identifier: CC0-1.0
cmake_minimum_required(VERSION 3.12)
project(simplyfile)
include(GNUInstallDirs)

file(GLOB_RECURSE CPP_SRCS src/simplyfile/*.cpp)

add_library(simplyfile SHARED ${CPP_SRCS})
add_library(simplyfile::simplyfile ALIAS simplyfile)

target_include_directories(simplyfile
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_LIST_DIR}/src/>
    $<INSTALL_INTERFACE:include>
)
target_compile_features(simplyfile PUBLIC cxx_std_17)

set(DEST_DIR "${CMAKE_INSTALL_PREFIX}")

execute_process(
        COMMAND git describe --tags
        OUTPUT_VARIABLE VERSION
        WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
        ERROR_VARIABLE VERSION_ERROR
        OUTPUT_STRIP_TRAILING_WHITESPACE)

if(VERSION STREQUAL "")
    message(FATAL_ERROR "cannot generate version string")
endif()

message("simplyfile version: " ${VERSION})

configure_file(simplyfile.pc.in simplyfile.pc @ONLY)

install(TARGETS simplyfile DESTINATION ${CMAKE_INSTALL_LIBDIR})
install(FILES
    src/simplyfile/Epoll.h
    src/simplyfile/Event.h
    src/simplyfile/FileDescriptor.h
    src/simplyfile/INotify.h
    src/simplyfile/Process.h
    src/simplyfile/SerialPort.h
    src/simplyfile/ThreadTime.h
    src/simplyfile/Timer.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME})
install(FILES
    src/simplyfile/socket/Host.h
    src/simplyfile/socket/Socket.h
    DESTINATION ${CMAKE_INSTALL_INCLUDEDIR}/${PROJECT_NAME}/socket)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/simplyfile.pc
    DESTINATION ${CMAKE_INSTALL_PREFIX}/share/pkgconfig)
install(DIRECTORY LICENSES
    DESTINATION ${CMAKE_INSTALL_PREFIX}/share/licenses/${PROJECT_NAME})
