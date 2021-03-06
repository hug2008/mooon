# Writed by yijian (eyjian@qq.com, eyjian@gmail.com)

include_directories(../../include)
include_directories(../../include/mooon)
link_directories(${CMAKE_CURRENT_SOURCE_DIR})
link_directories(${MOOON_HOME}/lib)

# libmooon_uniq.a
add_library(mooon_uniq STATIC uniq_id.cpp crc32.cpp)

# uniq_agent
add_executable(uniq_agent agent.cpp crc32.cpp)
target_link_libraries(uniq_agent libmooon.a libthrift.a pthread dl rt z)

if (MOOON_HAVE_MYSQL)
	# uniq_master
	add_executable(uniq_master master.cpp crc32.cpp)
	target_link_libraries(uniq_master libmooon.a libmysqlclient.a libthrift.a pthread dl rt)
endif ()

# uniq_stress
add_executable(uniq_stress uniq_stress.cpp)
target_link_libraries(uniq_stress libmooon_uniq.a libmooon.a libthrift.a pthread dl rt)

# uniq_test
add_executable(uniq_test uniq_test.cpp)
target_link_libraries(uniq_test libmooon_uniq.a libmooon.a libthrift.a pthread dl rt)

# master_cli
add_executable(master_cli master_cli.cpp)
target_link_libraries(master_cli libmooon_uniq.a libmooon.a libthrift.a pthread dl rt)

# 设置依赖关系
ADD_DEPENDENCIES(uniq_stress mooon_uniq)
ADD_DEPENDENCIES(master_cli mooon_uniq)
ADD_DEPENDENCIES(uniq_test mooon_uniq)

# CMAKE_INSTALL_PREFIX
install(
        TARGETS mooon_uniq
        DESTINATION lib
       )
install(
        DIRECTORY ../include/mooon/uniq_id       
        DESTINATION include/mooon
        PATTERN "uniq_id.h"
       )
if (MOOON_HAVE_MYSQL)
    install(
            TARGETS uniq_master
            DESTINATION bin
           )
endif ()
install(
        TARGETS uniq_agent uniq_stress uniq_test master_cli
        DESTINATION bin
       )
