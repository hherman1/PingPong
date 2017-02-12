set (CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/cmake")

if(NOT CMAKE_BUILD_TYPE)
  set( CMAKE_BUILD_TYPE Release CACHE STRING
       "Choose the type of build, options are: None Debug Release RelWithDebInfo
#MinSizeRel." FORCE)
endif()

if (CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
    set (CMAKE_INSTALL_PREFIX "${${PROJECT_NAME}_SOURCE_DIR}/build/install" CACHE PATH "default install path" FORCE )
endif()

set (SOURCEFILES  src/BaseApp.cpp
				  src/Box.cpp
				  src/Cone.cpp
				  src/Cylinder.cpp
				  src/Event.cpp
				  include/glad/src/glad.c
				  src/ExampleApp.cpp
				  src/GLSLProgram.cpp
				  src/Line.cpp
				  src/main.cpp
				  src/Mesh.cpp
				  src/Model.cpp
				  src/ModelRegistry.cpp
				  src/Sphere.cpp
				  src/Texture.cpp
				  src/Table.cpp
				  src/Clock.cpp)

set (HEADERFILES  src/BaseApp.h
				  src/Box.h
				  src/Cone.h
				  src/Cylinder.h
				  src/Event.h
				  src/ExampleApp.h
				  src/GLSLProgram.h
				  src/Line.h
				  src/Mesh.h
				  src/Model.h
				  src/ModelRegistry.h
				  src/Sphere.h
				  src/Texture.h
				  src/Table.h
				  src/Clock.h)



#file (GLOB SOURCEFILES "src/*.cpp" "include/glad/src/glad.c")
#file (GLOB HEADERFILES "src/*.h")

source_group("Header Files" FILES ${HEADERFILES})
############################################################

set (CMAKE_VERBOSE_MAKEFILE TRUE)

############################################################
#OS Specific flags should go here
############################################################

if (${CMAKE_SYSTEM_NAME} MATCHES "Linux")
 	 # Linux specific code
  	set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -fPIC") 
	find_package(Threads)
	find_package(X11)
	set(LIBS_ALL ${LIBS_ALL} ${CMAKE_THREAD_LIBS_INIT} rt Xrandr Xxf86vm Xi m dl ${X11_LIBRARIES})
endif (${CMAKE_SYSTEM_NAME} MATCHES "Linux")

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
  # Apple-specific code
  set(CMAKE_CXX_FLAGS "-DOSX")
	set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LANGUAGE_STANDARD "c++11")
	#set(CMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY "libc++")
	find_library(COCOA_LIB Cocoa)
	find_library(IOKIT_LIB IOKit)
	find_library(CORE_FOUNDATION_FRAMEWORK CoreFoundation)
    	find_library(CORE_VIDEO_FRAMEWORK CoreVideo)
	set(LIBS_ALL ${LIBS_ALL} ${COCOA_LIB} ${IOKIT_LIB} ${CORE_FOUNDATION_FRAMEWORK} ${CORE_VIDEO_FRAMEWORK})
	message(STATUS "${CORE_VIDEO_FRAMEWORK}")

endif( ${CMAKE_SYSTEM_NAME} MATCHES "Darwin")

if (WIN32)
  # Windows-specific
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    # Tell MSVC to use main instead of WinMain for Windows subsystem executables
    set_target_properties(${WINDOWS_BINARIES} PROPERTIES LINK_FLAGS "/ENTRY:mainCRTStartup")
endif (WIN32)


find_package(OpenGL REQUIRED)
set(OPENGL_LIBRARIES ${OPENGL_gl_LIBRARY})
set(OPENGL_INCLUDE_DIRS ${OPENGL_INCLUDE_DIR})

include_directories( ${GLFW_INCLUDE_DIR} ${ASSIMP_INCLUDE_DIR} ${SOIL_INCLUDE_DIR} ${OPENGL_INCLUDE_DIR} include/ include/glad/include)

add_executable(app ${SOURCEFILES} ${HEADERFILES})
target_link_libraries(app ${GLFW_LIBRARY} ${ZLIB_LIBRARIES} ${ASSIMP_LIBRARY} ${SOIL_LIBRARY} ${OPENGL_LIBRARIES} ${LIBS_ALL})
add_dependencies(app glfw assimp SOIL)


set_target_properties(app PROPERTIES VS_DEBUGGER_WORKING_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/Debug")

# Organize folder structure for Xcode, Visual Studio, etc.
set_property(GLOBAL PROPERTY USE_FOLDERS ON)

#copy resource files to build folder so that the executable can find them in the working directory
add_custom_command(TARGET app POST_BUILD
                   COMMAND ${CMAKE_COMMAND} -E copy_directory
                       ${CMAKE_CURRENT_SOURCE_DIR}/resources $<TARGET_FILE_DIR:app>)

# target_link_libraries(src glfw)
