cmake_minimum_required (VERSION 2.6)


project(graphics)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

file(MAKE_DIRECTORY ${PROJECT_SOURCE_DIR}/build)
set(BUILD_DIR ${PROJECT_SOURCE_DIR}/build)

file(MAKE_DIRECTORY libs)
add_subdirectory(libs)

file(MAKE_DIRECTORY app)
add_subdirectory(app)

set_property(DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} PROPERTY VS_STARTUP_PROJECT app)
