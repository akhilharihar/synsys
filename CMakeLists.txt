cmake_minimum_required(VERSION 3.13)

set(CMAKE_C_STANDARD 11)
set(CMAKE_CXX_STANDARD 17)

if(NOT DEFINED USE_DISPLAY)
    message(FATAL_ERROR "USE_DISPLAY var not set.")
endif()

if(NOT EXISTS ${CMAKE_CURRENT_LIST_DIR}/src/displays/${USE_DISPLAY}.cpp)
message(FATAL_ERROR "Display Driver for ${USE_DISPLAY} does not exist.")
endif()

execute_process(COMMAND git submodule update --init 
WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}
)

execute_process(COMMAND git submodule update --init 
WORKING_DIRECTORY ${CMAKE_CURRENT_LIST_DIR}/lib/pico-sdk
)

include(lib/pico-sdk/pico_sdk_init.cmake)

project(SynSYS 
VERSION 0.1
HOMEPAGE_URL "https://github.com/akhilharihar/synsys"
DESCRIPTION "SynSYS"
)

# initialize the Raspberry Pi Pico SDK
pico_sdk_init()

include_directories(include)
include_directories(lib)

add_subdirectory(lib/lvgl)
target_compile_definitions(lvgl PUBLIC LV_CONF_INCLUDE_SIMPLE)

add_executable(SynSYS src/synsys.cpp)

target_link_libraries(SynSYS
        pico_stdlib
        lvgl
        pico_multicore
        tinyusb_device
        pico_unique_id
        pico_time
        hardware_spi
)

pico_set_program_name(SynSYS SynSYS)
pico_set_program_version(SynSYS ${CMAKE_PROJECT_VERSION})
pico_set_program_url(SynSYS ${CMAKE_PROJECT_HOMEPAGE_URL})
pico_set_program_description(SynSYS ${CMAKE_PROJECT_DESCRIPTION})

pico_enable_stdio_uart(SynSYS 1)
pico_enable_stdio_usb(SynSYS 0)

pico_add_extra_outputs(SynSYS)