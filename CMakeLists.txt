idf_build_get_property(target IDF_TARGET)

# if(${target} STREQUAL "linux")
#     set(platform_srcs src/esp_modem_primitives_linux.cpp
#         src/esp_modem_uart_linux.cpp
#         src/esp_modem_netif_linux.cpp)
#     set(dependencies esp_system_protocols_linux)
# else()
#     set(platform_srcs src/esp_modem_primitives_freertos.cpp
#                       src/esp_modem_api_target.cpp
#                       src/esp_modem_uart.cpp
#                       src/esp_modem_term_uart.cpp
#                       src/esp_modem_netif.cpp)
#     set(dependencies driver esp_event esp_netif)
# endif()

file(GLOB_RECURSE SRC_DIR_LIST  "./*.c")

#自动查找头文件路径函数(没有去重)
macro(FIND_INCLUDE_DIR result curdir)                                   #定义函数,2个参数:存放结果result；指定路径curdir；
    file(GLOB_RECURSE children "${curdir}/*.hpp" "${curdir}/*.h" )	    #遍历获取{curdir}中*.hpp和*.h文件列表
    message(STATUS "children= ${children}")								#打印*.hpp和*.h的文件列表
    set(dirlist "")														#定义dirlist中间变量，并初始化
    foreach(child ${children})											#for循环
        string(REGEX REPLACE "(.*)/.*" "\\1" LIB_NAME ${child})			#字符串替换,用/前的字符替换/*h
        if(IS_DIRECTORY ${LIB_NAME})									#判断是否为路径
            LIST(APPEND dirlist ${LIB_NAME})							#将合法的路径加入dirlist变量中
        endif()															#结束判断
    endforeach()														#结束for循环
    set(${result} ${dirlist})											#dirlist结果放入result变量中
endmacro()																#函数结束

#查找include目录下的所有*.hpp,*.h头文件,并路径列表保存到 INCLUDE_DIR_LIST 变量中
# FIND_INCLUDE_DIR(INCLUDE_DIR_LIST ${PROJECT_SOURCE_DIR}/components/link_sdk)
# message(STATUS "------------- head files: ${INCLUDE_DIR_LIST}")
# "components/*.c" "core/*.c")
# add_library(${PROJECT_NAME} STATIC ${SRC_DIR_LIST})
# add_library(
#         ${PROJECT_NAME} STATIC
#         ${SRC_DIR_LIST}
# ) 

message(STATUS "SRC_DIR_LIST = ${SRC_DIR_LIST}")
# message(STATUS "SRC_DIR_LIST = ${SRC_DIR_LIST}")

set(srcs ${platform_srcs}
        # "core/aiot_mqtt_api.c"
        # "core/aiot_state_api.c"
        "${SRC_DIR_LIST}"
        )

set(include_dirs
                "core"
                "core/utils"
                "core/sysdep"
                "data-model"
                "ota"
                "subdev"
                )

idf_component_register(SRCS "${srcs}"
                    INCLUDE_DIRS "${include_dirs}"
                    # REQUIRES ${dependencies}
                    PRIV_REQUIRES mqtt json mbedtls
                    )


# set_target_properties(${COMPONENT_LIB} PROPERTIES
#     CXX_STANDARD 17
#     CXX_STANDARD_REQUIRED ON
#     CXX_EXTENSIONS ON
# )


# if(${target} STREQUAL "linux")
#     # This is needed for ESP_LOGx() macros, as integer formats differ on ESP32(..) and x64
#     set_target_properties(${COMPONENT_LIB} PROPERTIES COMPILE_FLAGS -Wno-format)
# endif()
