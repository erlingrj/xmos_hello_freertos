cmake_minimum_required(VERSION 3.14)

# Disable in-source build.
if("${CMAKE_SOURCE_DIR}" STREQUAL "${CMAKE_BINARY_DIR}")
    message(FATAL_ERROR "In-source build is not allowed! Please specify a build folder.\n\tex:cmake -B build")
endif()

## TODO: Specify configuration here
#set(USE_RTOS_GPIO_DRIVER TRUE)

## Import XMOS RTOS platform configuration.
## Must be done after setting the configuration options.
include("$ENV{XCORE_SDK_PATH}/tools/cmake_utils/xmos_rtos_platform.cmake")

## TODO: Set your project definition here
project(getting_started)
set(PROJECT_VERSION 1.0.0)

## Enable languages for project
enable_language(CXX C ASM)

## TODO: Modify the compiler flags as needed
set(APP_COMPILER_FLAGS
    "-Os"
    "-g"
    "-report"
    "-fxscope"
    "-mcmodel=large"
    "-Wno-xcore-fptrgroup"
    "${CMAKE_CURRENT_SOURCE_DIR}/src/config.xscope"
    "${CMAKE_CURRENT_SOURCE_DIR}/${BOARD}.xn"
)

set(APP_SOURCES
    "src/main.c"
    "src/platform/platform_init.c"
    "src/platform/platform_start.c"
    "src/platform/driver_instances.c"
    ## TODO: Add your other sources here
)

set(APP_INCLUDES
    "src"
    "src/platform"
)

if(${BOARD} STREQUAL "XCORE200-MIC-ARRAY")
    set(SYSCLK_FREQ 500)
else()
    set(SYSCLK_FREQ 700)
endif()

## TODO: Modify the compile definitions as needed
add_compile_definitions(
    DEBUG_PRINT_ENABLE=1
    PLATFORM_USES_TILE_0=1
    PLATFORM_USES_TILE_1=1
)

add_executable(${TARGET_NAME})

target_sources(${TARGET_NAME} PRIVATE ${APP_SOURCES} ${XMOS_RTOS_PLATFORM_SOURCES})
target_include_directories(${TARGET_NAME} PRIVATE ${APP_INCLUDES} ${XMOS_RTOS_PLATFORM_INCLUDES})

target_compile_options(${TARGET_NAME} PRIVATE ${APP_COMPILER_FLAGS})
target_link_options(${TARGET_NAME} PRIVATE ${APP_COMPILER_FLAGS})
