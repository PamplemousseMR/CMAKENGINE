#=======================================
#                PROJECT                
#=======================================

cmake_minimum_required(VERSION 3.0)
project(CMAKENGINE VERSION 1.0 LANGUAGES CXX)

#=======================================
#              EXECUTABLE               
#=======================================

# Create the executable.
add_executable(
	${PROJECT_NAME} 
	CMAKENGINE/CMAKENGINE.cpp
	)

#=======================================
#                LIBRARY                
#=======================================

find_package(ModuleA REQUIRED)

target_link_libraries(${PROJECT_NAME} PRIVATE ModuleA::ModuleA)

#target_include_directories(${PROJECT_NAME} PRIVATE ${MODULEA_INCLUDE_DIRS})
#target_link_libraries(${PROJECT_NAME} PRIVATE ${MODULEA_LIBRARIES})

#=======================================
#                INSTALL
#=======================================

set(CMAKENGINE_RUNTIME_DESTINATION "bin")

# Install the library.
install(
        TARGETS ${PROJECT_NAME}
        DESTINATION ${CMAKENGINE_RUNTIME_DESTINATION}
        )
