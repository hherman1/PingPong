include(ExternalProject)
set(soil_checkout_dir ${PROJECT_SOURCE_DIR}/deps/SOIL)
make_directory(${soil_checkout_dir})

set(soil_patch_dir ${CMAKE_CURRENT_SOURCE_DIR}/src)

set(soil_build_dir ${LIB_BUILD_DIR}/soil)
set(soil_stamp_dir ${soil_build_dir}/stamp)
set(soil_tmp_dir ${soil_build_dir}/tmp)

set(soil_PREFIX "${soil_checkout_dir}")
set(soil_INSTALL_DIR "${soil_build_dir}/install")

set(soil_PATCH ${CMAKE_COMMAND} -E copy ${soil_patch_dir}/CMakeLists.txt <SOURCE_DIR>/CMakeLists.txt)
set(soil_CMAKE_ARGS "-DCMAKE_INSTALL_PREFIX=${soil_INSTALL_DIR}")
   
ExternalProject_add(SOIL
	PREFIX ${soil_PREFIX}
	URL http://www.lonesock.net/files/soil.zip
	INSTALL_DIR ${soil_INSTALL_DIR}
	CMAKE_ARGS ${soil_CMAKE_ARGS}
	PATCH_COMMAND ${soil_PATCH}
	UPDATE_COMMAND ""
	DOWNLOAD_DIR ${soil_checkout_dir}
	SOURCE_DIR ${soil_checkout_dir}
	TMP_DIR ${soil_tmp_dir}
	STAMP_DIR ${soil_stamp_dir}
	BINARY_DIR ${soil_build_dir}
 )

set(SOIL_INCLUDE_DIR ${soil_INSTALL_DIR}/include CACHE INTERNAL "Directory of SOIL header files")

if(MSVC)
	set(SOIL_LIBRARY ${soil_INSTALL_DIR}/lib/SOIL.lib CACHE INTERNAL "SOIL lib file")
else()
	set(SOIL_LIBRARY ${soil_INSTALL_DIR}/lib/libSOIL.a CACHE INTERNAL "SOIL lib file")
endif()	