cmake_minimum_required(VERSION 3.0)
project(crash-handler)

#############################
# CMake Settings
#############################

SET(NODEJS_URL "https://atom.io/download/atom-shell" CACHE STRING "Node.JS URL")
SET(NODEJS_NAME "iojs" CACHE STRING "Node.JS Name")
SET(NODEJS_VERSION "v2.0.8" CACHE STRING "Node.JS Version")

include(NodeJS)
include(CPack)

nodejs_init()

#############################
# Source, Libraries & Directories
#############################
SET(PROJECT_SOURCE 
	"${PROJECT_SOURCE_DIR}/module.cpp"
)

SET(PROJECT_LIBRARIES
	${NODEJS_LIBRARIES}
)

SET(PROJECT_INCLUDE_PATHS
	${NODEJS_INCLUDE_DIRS}
	"${CMAKE_SOURCE_DIR}/source/"
)

#############################
# Building
#############################
ADD_LIBRARY(
	${PROJECT_NAME}
	MODULE
	${PROJECT_SOURCE}
)

TARGET_LINK_LIBRARIES(
	${PROJECT_NAME}
	${PROJECT_LIBRARIES}
)

TARGET_INCLUDE_DIRECTORIES(
	${PROJECT_NAME}
	PUBLIC ${PROJECT_INCLUDE_PATHS}
)

SET_TARGET_PROPERTIES(
	${PROJECT_NAME}
	PROPERTIES
	PREFIX ""
	SUFFIX ".node"
)

target_compile_definitions(
	${PROJECT_NAME}
	PRIVATE BUILDING_NODE_EXTENSION
)

#############################
# Distribute
#############################
INSTALL(TARGETS ${PROJECT_NAME}
	RUNTIME DESTINATION "./" COMPONENT Runtime
	LIBRARY DESTINATION "./" COMPONENT Runtime
)
INSTALL(FILES $<TARGET_PDB_FILE:${PROJECT_NAME}> DESTINATION ./ OPTIONAL)


# JavaScript
INSTALL(
	FILES "${PROJECT_SOURCE_DIR}/package.json"
	DESTINATION "./"
)
INSTALL(
	FILES "${PROJECT_SOURCE_DIR}/main.js"
	DESTINATION "./"
)
