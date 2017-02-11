set(glfw_checkout_Dir ${PROJECT_SOURCE_DIR}/deps/glfw)
make_directory(${glfw_checkout_Dir})
include(ExternalProject)

set(glfw_build_dir ${LIB_BUILD_DIR}/glfw)
set(glfw_stamp_dir ${glfw_build_dir}/stamp)
set(glfw_tmp_dir ${glfw_build_dir}/tmp)

set(glfw_PREFIX "${glfw_checkout_Dir}")
set(glfw_INSTALL_DIR "${glfw_build_dir}/install")

set(glfw_CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${glfw_INSTALL_DIR} -DGLFW_BUILD_DOCS=OFF —DGLFW_BUILD_TESTS=OFF -DCMAKE_BUILD_TYPE=${CMAKE_BUILD_TYPE})
set(glfw_DIR "${glfw_INSTALL_DIR}")

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
	set(glfw_CMAKE_ARGS ${glfw_CMAKE_ARGS} -DCMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LANGUAGE_STANDARD="c++11" -DCMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LIBRARY="libc++" -DGLFW_USE_RETINA=ON)
endif()

if (WIN32)
    set(glfw_CMAKE_ARGS ${glfw_CMAKE_ARGS} -DUSE_MSVC_RUNTIME_LIBRARY_DLL=OFF -DGLFW_USE_HYBRID_HPG=ON)
endif()
   
ExternalProject_add(glfw
		PREFIX ${glfw_PREFIX} 
		GIT_REPOSITORY https://github.com/glfw/glfw.git 
		INSTALL_DIR ${glfw_INSTALL_DIR}
		CMAKE_ARGS ${glfw_CMAKE_ARGS}
		UPDATE_COMMAND ""
		DOWNLOAD_DIR ${glfw_checkout_Dir}
		SOURCE_DIR ${glfw_checkout_Dir}
		TMP_DIR ${glfw_tmp_dir}
		STAMP_DIR ${glfw_stamp_dir}
		BINARY_DIR ${glfw_build_dir}
)
 
#set_property(TARGET glfw PROPERTY FOLDER "Dependencies")

set(GLFW_INCLUDE_DIR ${glfw_INSTALL_DIR}/include CACHE INTERNAL "Directory of GLFW header files")
if(MSVC)
	set(GLFW_LIBRARY ${glfw_INSTALL_DIR}/lib/glfw3.lib CACHE INTERNAL "GLFW lib file")
else()
	set(GLFW_LIBRARY ${glfw_INSTALL_DIR}/lib/libglfw3.a CACHE INTERNAL "GLFW lib file")
endif()	