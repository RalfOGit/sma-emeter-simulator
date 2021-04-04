project(sma-emeter-simulator)

cmake_minimum_required(VERSION 3.10)

set(CMAKE_CXX_STANDARD 11)

# This CMake configuration assumes the following directory layout
#
# - speedwire-router
#   - libspeedwire
#   - <relative build path>

# determine relative build and install paths
message("CMAKE_BUILD_TYPE ${CMAKE_BUILD_TYPE}")
message("CMAKE_BINARY_DIR ${CMAKE_BINARY_DIR}")
message("CMAKE_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}")

string(REPLACE "${CMAKE_SOURCE_DIR}/" "" RELATIVE_BUILD_PATH "${CMAKE_BINARY_DIR}")
message("RELATIVE_BUILD_PATH ${RELATIVE_BUILD_PATH}")
string(REPLACE "${CMAKE_SOURCE_DIR}/" "" RELATIVE_INSTALL_PATH "${CMAKE_INSTALL_PREFIX}")
message("RELATIVE_INSTALL_PATH ${RELATIVE_INSTALL_PATH}")

# path definitions for libspeedwire (adapt to your needs)
set(SPEEDWIRE_DIR         "${CMAKE_SOURCE_DIR}/libspeedwire")
set(SPEEDWIRE_INCLUDE_DIR "${SPEEDWIRE_DIR}/include")
set(SPEEDWIRE_LIBRARY_DIR "${CMAKE_SOURCE_DIR}/${RELATIVE_BUILD_PATH}/libspeedwire")
add_subdirectory("libspeedwire")

# project sources and include path
set(PROJECT_SOURCES
    src/main.cpp
)
set(PROJECT_INCLUDE_DIR ${CMAKE_SOURCE_DIR}/include)

# build configuration
include_directories(${PROJECT_INCLUDE_DIR} ${SPEEDWIRE_INCLUDE_DIR})
link_directories(${SPEEDWIRE_LIBRARY_DIR})

add_executable(${PROJECT_NAME} ${PROJECT_SOURCES})
add_dependencies(${PROJECT_NAME} speedwire)

if (MSVC)
target_link_libraries(${PROJECT_NAME} speedwire.lib ws2_32.lib Iphlpapi.lib)
else()
target_link_libraries(${PROJECT_NAME} libspeedwire.a)
endif()

set_target_properties(${PROJECT_NAME}
    PROPERTIES OUTPUT_NAME ${PROJECT_NAME}
)
install(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION ${CMAKE_INSTALL_PREFIX})
