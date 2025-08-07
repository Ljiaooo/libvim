cmake_minimum_required(VERSION 3.17)
project(libvim NONE)

set(LIBVIMSRC_DIR ${CMAKE_CURRENT_SOURCE_DIR}/src)
set(LIBVIMINSTALL_DIR ${CMAKE_CURRENT_SOURCE_DIR}/install)

add_custom_command(
    OUTPUT ${LIBVIMSRC_DIR}/libvim.a
    COMMAND "C:/Program Files/Git/git-bash.exe" -C 
    "cd ${LIBVIMSRC_DIR} && make -f ${LIBVIMSRC_DIR}/Make_cyg_ming.mak installlibvim CC=${CMAKE_C_COMPILER} CXX=${CMAKE_CXX_COMPILER} AR=${CMAKE_AR} DESTDIR=${LIBVIMINSTALL_DIR} OUTDIR=${LIBVIMSRC_DIR}/obj_files CFLAGS=-Wno-error=implicit-const-int-float-conversion -Iproto"
    COMMENT "Building libvim using Makefile..."
)

add_custom_target(libvim ALL
    DEPENDS ${LIBVIMSRC_DIR}/libvim.a
)

set(LIBVIM_INCLUDE_DIRS
    ${LIBVIMINSTALL_DIR}
    ${LIBVIMINSTALL_DIR}/proto
)

add_library(libvim_static STATIC IMPORTED GLOBAL)
set_target_properties(libvim_static PROPERTIES
    IMPORTED_LOCATION ${LIBVIMINSTALL_DIR}/libvim.a
    INTERFACE_INCLUDE_DIRECTORIES "${LIBVIM_INCLUDE_DIRS}"
)

add_dependencies(libvim_static libvim)
