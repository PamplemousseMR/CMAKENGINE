#=======================================
#                PROJECT                
#=======================================

cmake_minimum_required(VERSION 3.16)
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

#=======================================
#                INSTALL
#=======================================

set(CMAKENGINE_RUNTIME_DESTINATION "bin")

# Install the executable.
install(
    TARGETS ${PROJECT_NAME}
    DESTINATION ${CMAKENGINE_RUNTIME_DESTINATION}
    )

# Install shared libraries
install(
    CODE [[
        file(GET_RUNTIME_DEPENDENCIES
            EXECUTABLES $<TARGET_FILE:CMAKENGINE>
            RESOLVED_DEPENDENCIES_VAR CMAKENGINE_RDEP
            UNRESOLVED_DEPENDENCIES_VAR CMAKENGINE_UDEP
            DIRECTORIES ${CMAKE_PREFIX_PATH}/bin
            POST_EXCLUDE_REGEXES system32|/x86_64-linux-gnu
            )

        file(INSTALL
            DESTINATION "${CMAKE_INSTALL_PREFIX}/bin"
            TYPE SHARED_LIBRARY
            FILES ${CMAKENGINE_RDEP}
            )
    ]]
)
