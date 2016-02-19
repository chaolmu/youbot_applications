cmake_minimum_required(VERSION 2.8.3)
project(youbot_joint_configurator)

find_package(catkin REQUIRED COMPONENTS youbot_driver)
include_directories(${catkin_INCLUDE_DIRS})
link_directories(${catkin_LIBRARY_DIRS})
catkin_package()


IF(NOT DEFINED JOINT_CONFIGURATOR_CONFIG_DIR)
  SET(JOINT_CONFIGURATOR_CONFIG_DIR ${PROJECT_SOURCE_DIR}/config/)
ENDIF(NOT DEFINED JOINT_CONFIGURATOR_CONFIG_DIR)

add_definitions(-DYOUBOT_CONFIGURATIONS_DIR="${youbot_driver_CONFIG_DIR}")
add_definitions(-DCONFIG_DIR="${JOINT_CONFIGURATOR_CONFIG_DIR}")


SET(YOUBOT_DRIVER_CONFIG
${PROJECT_SOURCE_DIR}/config/arm-1.cfg
${PROJECT_SOURCE_DIR}/config/arm-2.cfg
${PROJECT_SOURCE_DIR}/config/arm-3.cfg
${PROJECT_SOURCE_DIR}/config/arm-4.cfg
${PROJECT_SOURCE_DIR}/config/arm-5.cfg
${PROJECT_SOURCE_DIR}/config/arm-1-protected.cfg
${PROJECT_SOURCE_DIR}/config/arm-2-protected.cfg
${PROJECT_SOURCE_DIR}/config/arm-3-protected.cfg
${PROJECT_SOURCE_DIR}/config/arm-4-protected.cfg
${PROJECT_SOURCE_DIR}/config/arm-5-protected.cfg
${PROJECT_SOURCE_DIR}/config/base.cfg
${PROJECT_SOURCE_DIR}/config/base-protected.cfg
)

add_executable(youBot_JointConfigurator
  src/JointConfiguratorMain.cpp
)

add_executable(youBot_ArmConfigurator
  src/ArmConfigurator.cpp
)

add_executable(youBot_BaseConfigurator
  src/BaseConfigurator.cpp
)

add_library(JointConfigurator
  src/JointConfigurator.cpp
)


TARGET_LINK_LIBRARIES(youBot_JointConfigurator
    ${catkin_LIBRARIES}
	pthread
	JointConfigurator
)

TARGET_LINK_LIBRARIES(youBot_ArmConfigurator
	${catkin_LIBRARIES}
	pthread
	JointConfigurator
)

TARGET_LINK_LIBRARIES(youBot_BaseConfigurator
	${catkin_LIBRARIES}
	pthread
	JointConfigurator
)




OPTION(USE_SETCAP "Set permissions to access ethernet interface without sudo" ON)

SET(SUDO_COMMAND sudo)

IF(USE_SETCAP)
add_custom_command(TARGET youBot_JointConfigurator POST_BUILD
    COMMAND ${SUDO_COMMAND} setcap cap_net_raw+ep $<TARGET_FILE:youBot_JointConfigurator>
)

add_custom_command(TARGET youBot_ArmConfigurator POST_BUILD
    COMMAND ${SUDO_COMMAND} setcap cap_net_raw+ep $<TARGET_FILE:youBot_ArmConfigurator>
)

add_custom_command(TARGET youBot_BaseConfigurator POST_BUILD
    COMMAND ${SUDO_COMMAND} setcap cap_net_raw+ep $<TARGET_FILE:youBot_BaseConfigurator>
)    
endif(USE_SETCAP)

