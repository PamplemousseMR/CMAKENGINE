#=======================================
#                PROJECT                
#=======================================

cmake_minimum_required(VERSION 3.0)
project(ModuleA VERSION 1.0 LANGUAGES CXX)

#=======================================
#                SETTINGS               
#=======================================

option(BUILD_SHARED_LIBS "If present and true, this will cause all libraries to be built shared unless the library was explicitly added as a static library" OFF)

set(MODULEA_TARGET_NAME ${PROJECT_NAME})

# Set the C standard.
set(CMAKE_C_STANDARD_REQUIRED ON)
set(C_STANDARD 11)

# Set standard installation directories.
set(MODULEA_RUNTIME_DESTINATION "bin")
set(MODULEA_LIBRARY_DESTINATION "lib")
set(MODULEA_ARCHIVE_DESTINATION "lib")
set(MODULEA_INCLUDES_DIR "include")
set(MODULEA_INCLUDES_DESTINATION "${MODULEA_INCLUDES_DIR}/${PROJECT_NAME}")

# Set CMake targets name, namespace and install directory.
set(MODULEA_CMAKE_TARGETS_EXPORT_NAME "${PROJECT_NAME}Targets")
set(MODULEA_CMAKE_TARGETS_NAMESPACE "${PROJECT_NAME}::")

# CMake config files name.
set(MODULEA_CMAKE_VERSION_CONFIG_FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}ConfigVersion.cmake")
set(MODULEA_CMAKE_PROJECT_CONFIG_FILE "${CMAKE_CURRENT_BINARY_DIR}/${PROJECT_NAME}Config.cmake")

# CMake config files destination.
if(WIN32 AND NOT CYGWIN)
    set(MODULEA_CMAKE_CONFIG_DESTINATION "CMake")
else()
    set(MODULEA_CMAKE_CONFIG_DESTINATION "${MODULEA_LIBRARY_DESTINATION}/cmake/${PROJECT_NAME}")
endif()

#=======================================
#                LIBRARY                
#=======================================

# Store header files.
set(
    MODULEA_HEADER_FILES
    moduleA/moduleA.hpp
    )

# Create the library (headers are here since some IDE need them here).
add_library(
    ${MODULEA_TARGET_NAME}
    moduleA/moduleA.cpp
    ${MODULEA_HEADER_FILES}
    )

# Generate the export header
set(MODULEA_GENERATED_DIR "${CMAKE_CURRENT_BINARY_DIR}/generated")
file(MAKE_DIRECTORY ${MODULEA_GENERATED_DIR})

set(MODULEA_EXPORT_FILE_NAME "${MODULEA_GENERATED_DIR}/${MODULEA_TARGET_NAME}_export.h")

include(GenerateExportHeader)
generate_export_header(
    ${MODULEA_TARGET_NAME}
    EXPORT_FILE_NAME ${MODULEA_EXPORT_FILE_NAME}
    EXPORT_MACRO_NAME "MODULEA_EXPORT"
)

# Add include directory (includes may not be at the same place between build and install).
target_include_directories(
    ${MODULEA_TARGET_NAME}
    PUBLIC
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>
    $<BUILD_INTERFACE:${MODULEA_GENERATED_DIR}>
    $<INSTALL_INTERFACE:${MODULEA_INCLUDES_DIR}>
)

if(${BUILD_SHARED_LIBS})
    target_compile_definitions(${MODULEA_TARGET_NAME} PRIVATE "ModuleA_EXPORTS")
else()
    target_compile_definitions(${MODULEA_TARGET_NAME} PRIVATE "MODULEA_STATIC_DEFINE")
endif()

#=======================================
#                CONFIG                 
#=======================================

# Include write_basic_package_version_file and configure_package_config_file.
include(CMakePackageConfigHelpers)

# Generate basic config version files (ModuleAConfigVersion.cmake).
write_basic_package_version_file(
    ${MODULEA_CMAKE_VERSION_CONFIG_FILE} 
    VERSION ${PROJECT_VERSION} 
    COMPATIBILITY SameMajorVersion
)

# Configure the config.cmake.in
# All variables in PATH_VARS will be transformed into PACKAGE_<var...> with a path relative to the installed location of the package.
configure_package_config_file(
	"${CMAKE_SOURCE_DIR}/cmake/ModuleAConfig.cmake.in" ${MODULEA_CMAKE_PROJECT_CONFIG_FILE} 
	INSTALL_DESTINATION  ${MODULEA_CMAKE_CONFIG_DESTINATION}
	PATH_VARS MODULEA_INCLUDES_DIR MODULEA_CMAKE_CONFIG_DESTINATION MODULEA_LIBRARY_DESTINATION
	)

# Install cmake config files (ModuleAConfigVersion.cmake and ModuleAConfig.cmake).
install(
    FILES ${MODULEA_CMAKE_VERSION_CONFIG_FILE} ${MODULEA_CMAKE_PROJECT_CONFIG_FILE}
    DESTINATION ${MODULEA_CMAKE_CONFIG_DESTINATION}
    )

# Install cmake targets files (ModuleATargets.cmake and ModuleATargets-debug.cmake).
install(
    EXPORT ${MODULEA_CMAKE_TARGETS_EXPORT_NAME}
    NAMESPACE ${MODULEA_CMAKE_TARGETS_NAMESPACE}
    DESTINATION ${MODULEA_CMAKE_CONFIG_DESTINATION}
    )

#=======================================
#                INSTALL                
#=======================================

# Install the library.
install(
        FILES
            ${MODULEA_HEADER_FILES}
            ${MODULEA_EXPORT_FILE_NAME}
        DESTINATION ${MODULEA_INCLUDES_DESTINATION}
	)

# The EXPORT is required to export your library as a COMPONENTS (ModuleA::ModuleA)
install(
	TARGETS ${MODULEA_TARGET_NAME}
        EXPORT ${MODULEA_CMAKE_TARGETS_EXPORT_NAME}
	RUNTIME DESTINATION ${MODULEA_RUNTIME_DESTINATION}
	LIBRARY DESTINATION ${MODULEA_LIBRARY_DESTINATION}
	ARCHIVE DESTINATION ${MODULEA_ARCHIVE_DESTINATION}
	)
