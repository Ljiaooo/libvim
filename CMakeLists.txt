cmake_minimum_required(VERSION 3.17)
project(libvim NONE)

set(LIBVIMSRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
find_program(BASH_EXE bash)
get_filename_component(C_COMPILER_NAME ${CMAKE_C_COMPILER} NAME_WE)
get_filename_component(AR_NAME ${CMAKE_AR} NAME_WE)

file(WRITE ${CMAKE_CURRENT_BINARY_DIR}/build_libvim.sh
"#!/bin/bash
set -e
cd '${LIBVIMSRC_DIR}'
make -f Make_cyg_ming.mak installlibvim \\
    CC='${C_COMPILER_NAME}'  \\
    AR='${AR_NAME}' \\
    OUTDIR='${LIBVIMSRC_DIR}/generated_objs' \\
    CFLAGS='-w -Iproto'
")

add_custom_command(
    OUTPUT ${LIBVIMSRC_DIR}/libvim.a
    COMMAND ${BASH_EXE} ${CMAKE_CURRENT_BINARY_DIR}/build_libvim.sh
    WORKING_DIRECTORY ${LIBVIMSRC_DIR}
    COMMENT "Building libvim using Makefile..."
    USES_TERMINAL
    VERBATIM
)

add_custom_target(libvim ALL
    DEPENDS ${LIBVIMSRC_DIR}/libvim.a
)

set(LIBVIM_INCLUDE_DIRS
    ${LIBVIMSRC_DIR}
    ${LIBVIMSRC_DIR}/proto
)

add_library(libvim_static STATIC IMPORTED GLOBAL)
set_target_properties(libvim_static PROPERTIES
    IMPORTED_LOCATION ${LIBVIMSRC_DIR}/libvim.a
    INTERFACE_INCLUDE_DIRECTORIES "${LIBVIM_INCLUDE_DIRS}"
)

add_dependencies(libvim_static libvim)
