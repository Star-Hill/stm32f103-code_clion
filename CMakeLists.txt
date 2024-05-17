# THIS FILE IS AUTO GENERATED FROM THE TEMPLATE! DO NOT CHANGE!

set(CMAKE_SYSTEM_NAME Generic)  # 设置CMake系统名称为Generic。
set(CMAKE_SYSTEM_VERSION 1)     # 设置CMake系统版本为1。
cmake_minimum_required(VERSION 3.17)  # 设置CMake最低版本要求为3.17。

# specify cross compilers and tools
set(CMAKE_C_COMPILER arm-none-eabi-gcc)     # 设置C编译器为arm-none-eabi-gcc。
set(CMAKE_CXX_COMPILER arm-none-eabi-g++)   # 设置C++编译器为arm-none-eabi-g++。
set(CMAKE_ASM_COMPILER  arm-none-eabi-gcc)  # 设置汇编语言编译器为arm-none-eabi-gcc。
set(CMAKE_AR arm-none-eabi-ar)              # 设置静态库打包工具为arm-none-eabi-ar。
set(CMAKE_OBJCOPY arm-none-eabi-objcopy)    # 设置目标文件转换工具为arm-none-eabi-objcopy。
set(CMAKE_OBJDUMP arm-none-eabi-objdump)    # 设置目标文件反汇编工具为arm-none-eabi-objdump。
set(SIZE arm-none-eabi-size)                # 设置查看目标文件大小的工具为arm-none-eabi-size。
set(CMAKE_TRY_COMPILE_TARGET_TYPE STATIC_LIBRARY)   # 设置尝试编译的目标类型为静态库。

# project settings
project(Demo C CXX ASM)  # 指定项目名称为Demo，并使用C、C++、汇编语言进行编译。
set(CMAKE_CXX_STANDARD 17)  # 设置C++标准为C++17。
set(CMAKE_C_STANDARD 11)     # 设置C标准为C11。

#Uncomment for hardware floating point
#add_compile_definitions(ARM_MATH_CM4;ARM_MATH_MATRIX_CHECK;ARM_MATH_ROUNDING)
#add_compile_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)
#add_link_options(-mfloat-abi=hard -mfpu=fpv4-sp-d16)

#Uncomment for software floating point
#add_compile_options(-mfloat-abi=soft)

add_compile_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)  # 添加编译选项，设置目标处理器为Cortex-M3，并启用Thumb指令集。
add_compile_options(-ffunction-sections -fdata-sections -fno-common -fmessage-length=0)  # 添加编译选项，设置函数和数据的段信息，并禁用共享数据。

# uncomment to mitigate c++17 absolute addresses warnings
#set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Wno-register")

if ("${CMAKE_BUILD_TYPE}" STREQUAL "Release")
message(STATUS "Maximum optimization for speed")  # 输出信息，指示正在进行最大化速度优化。
add_compile_options(-Ofast)  # 添加编译选项，进行最大化速度优化。
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "RelWithDebInfo")
message(STATUS "Maximum optimization for speed, debug info included")  # 输出信息，指示正在进行最大化速度优化并包含调试信息。
add_compile_options(-Ofast -g)  # 添加编译选项，进行最大化速度优化并包含调试信息。
elseif ("${CMAKE_BUILD_TYPE}" STREQUAL "MinSizeRel")
message(STATUS "Maximum optimization for size")  # 输出信息，指示正在进行最大化大小优化。
add_compile_options(-Os)  # 添加编译选项，进行最大化大小优化。
else ()
message(STATUS "Minimal optimization, debug info included")  # 输出信息，指示正在进行最小化优化并包含调试信息。
add_compile_options(-Og -g)  # 添加编译选项，进行最小化优化并包含调试信息。
endif ()

include_directories(Core/Inc Drivers/STM32F1xx_HAL_Driver/Inc Drivers/STM32F1xx_HAL_Driver/Inc/Legacy Drivers/CMSIS/Device/ST/STM32F1xx/Include Drivers/CMSIS/Include)  # 添加包含目录。

add_definitions(-DDEBUG -DUSE_HAL_DRIVER -DSTM32F103xB)  # 添加预定义宏。

file(GLOB_RECURSE SOURCES "Core/*.*" "Drivers/*.*")  # 使用通配符获取源文件列表。

set(LINKER_SCRIPT ${CMAKE_SOURCE_DIR}/STM32F103C8TX_FLASH.ld)  # 设置链接脚本路径。

add_link_options(-Wl,-gc-sections,--print-memory-usage,-Map=${PROJECT_BINARY_DIR}/${PROJECT_NAME}.map)  # 添加链接选项，控制链接器行为。
add_link_options(-mcpu=cortex-m3 -mthumb -mthumb-interwork)  # 添加链接选项，设置目标处理器为Cortex-M3，并启用Thumb指令集。
add_link_options(-T ${LINKER_SCRIPT})  # 添加链接选项，指定链接脚本。

add_executable(${PROJECT_NAME}.elf ${SOURCES} ${LINKER_SCRIPT})  # 创建可执行文件。

set(HEX_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.hex)  # 设置hex文件路径。
set(BIN_FILE ${PROJECT_BINARY_DIR}/${PROJECT_NAME}.bin)  # 设置bin文件路径。

add_custom_command(TARGET ${PROJECT_NAME}.elf POST_BUILD
COMMAND ${CMAKE_OBJCOPY} -Oihex $<TARGET_FILE:${PROJECT_NAME}.elf> ${HEX_FILE}  # 添加自定义命令，生成hex文件。
COMMAND ${CMAKE_OBJCOPY} -Obinary $<TARGET_FILE:${PROJECT_NAME}.elf> ${BIN_FILE}  # 添加自定义命令，生成bin文件。
COMMENT "Building ${HEX_FILE}
Building ${BIN_FILE}")  # 输出信息，指示正在生成hex和bin文件。
