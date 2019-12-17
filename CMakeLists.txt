cmake_minimum_required(VERSION 3.0)
cmake_policy(SET CMP0054 NEW)
project(neon_curses)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)

set(CMAKE_LIBRARY_OUTPUT_DIRECTORY "")

if ("${CMAKE_SYSTEM_NAME}" STREQUAL "Windows")
    add_custom_command(
        OUTPUT PDCurses-3.4/win32/vcwin32.mak
        COMMAND tar zxvCf ${CMAKE_CURRENT_SOURCE_DIR} PDCurses-3.4.tar.gz
        DEPENDS PDCurses-3.4.tar.gz
    )
    add_custom_target(
        curses
        COMMAND cd ${CMAKE_CURRENT_SOURCE_DIR}/PDCurses-3.4/win32 && nmake -fvcwin32.mak WIDE=Y UTF8=Y DEBUG=Y
        DEPENDS PDCurses-3.4/win32/vcwin32.mak
    )
    set(CURSES_LIB "PDCurses-3.4/win32/pdcurses")
    set(CURSES_INC "PDCurses-3.4")
else()
    find_library(
        ncurses
        NAMES ncurses libncurses
        PATHS lib
    )
    set(CURSES_LIB ${ncurses})
    set(CURSES_INC "")
endif()

if ("${CMAKE_CXX_COMPILER_ID}" STREQUAL "MSVC")
else()
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wall -Wextra -Wno-unused-parameter -g")
endif()
add_library(neon_curses SHARED curses.cpp)
if ("${CMAKE_SYSTEM_NAME}" STREQUAL "Windows")
    add_dependencies(neon_curses curses)
endif()
target_include_directories(neon_curses PRIVATE ${CURSES_INC})
target_link_libraries(neon_curses ${CURSES_LIB})
