project(ubuntu-touch-sdl-template C CXX)
cmake_minimum_required(VERSION 2.8.9)

# Do not remove this line, its required for the correct functionality of the Ubuntu-SDK
set(UBUNTU_MANIFEST_PATH "manifest.json.in" CACHE INTERNAL "Tells QtCreator location and name of the manifest file")
set (CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake")

# Automatically create moc files
set(CMAKE_AUTOMOC ON)

set(APP_NAME          sdl2-gles2-template)
set(MAIN              ${APP_NAME})
set(ICON              "sdl2-gles2-template.png")

# Set install paths
set(CMAKE_INSTALL_PREFIX /)
set(DATA_DIR /)
set(DESKTOP_DIR ${DATA_DIR})
set(DESKTOP_FILE_NAME "sdl2-gles2-template.desktop")

set(EXEC "${APP_NAME}")

# This command figures out the target architecture for use in the manifest file
execute_process(
  COMMAND dpkg-architecture -qDEB_HOST_ARCH
  OUTPUT_VARIABLE CLICK_ARCH
  OUTPUT_STRIP_TRAILING_WHITESPACE
)

configure_file(manifest.json.in ${CMAKE_CURRENT_BINARY_DIR}/manifest.json)
install(FILES ${CMAKE_CURRENT_BINARY_DIR}/manifest.json
        DESTINATION ${CMAKE_INSTALL_PREFIX})
install(FILES ${DESKTOP_FILE_NAME}
        DESTINATION ${CMAKE_INSTALL_PREFIX})

install(FILES ${ICON} DESTINATION ${DATA_DIR})
install(FILES "sdl2-gles2-template.apparmor" DESTINATION ${DATA_DIR})

# Add source files from src/
set(
    EXEC_SOURCES
    src/main.c
)

# Set include path
set(CMAKE_INCLUDE_PATH "include")

# Add and install binary
add_executable(${EXEC} ${EXEC_SOURCES})

install_targets(${CMAKE_INSTALL_PREFIX} ${EXEC})

# Add the path to our local libraries
set(LIBRARY_SEARCH_PATHS "lib/arm-linux-gnueabihf/")

# Link against the correct libSDL2
if("${CLICK_ARCH}" STREQUAL "armhf")
    find_library(SDL2_LIB NAMES SDL2 PATHS ${LIBRARY_SEARCH_PATHS})
    find_library(SDL2_TEST_LIB NAMES SDL2_test PATHS ${LIBRARY_SEARCH_PATHS})
    set(EXEC_LIBS ${SDL2_LIB} ${SDL2_TEST_LIB})
else()
    set(EXEC_LIBS SDL2 SDL2_test)
endif("${CLICK_ARCH}" STREQUAL "armhf")

# List of libraries
target_link_libraries(${EXEC} ${EXEC_LIBS} m GLESv2 EGL)

# Include headers
add_subdirectory(include)

# Include dynamic libraries
add_subdirectory(lib)

add_custom_target("check" ${EXEC}
                    DEPENDS ${EXEC}
                    WORKING_DIRECTORY .)

add_custom_target("run" ${EXEC}
                    DEPENDS ${EXEC}
                    WORKING_DIRECTORY .)

# No op custom target for all not compiled files, so they show up in the QtCreator project tree
add_custom_target("ubuntu-touch-sdl-template_ClickFiles" ALL SOURCES ${DESKTOP_FILE_NAME} "sdl2-gles2-template.apparmor" "manifest.json.in")
