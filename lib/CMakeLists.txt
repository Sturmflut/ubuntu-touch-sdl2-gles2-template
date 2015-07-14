if("${CLICK_ARCH}" STREQUAL "armhf")
    add_subdirectory(arm-linux-gnueabihf)
endif("${CLICK_ARCH}" STREQUAL "armhf")
