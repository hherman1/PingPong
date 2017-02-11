set(assimp_checkout_Dir ${PROJECT_SOURCE_DIR}/deps/assimp)
make_directory(${assimp_checkout_Dir})
include(ExternalProject)

set(assimp_build_dir ${LIB_BUILD_DIR}/assimp)
set(assimp_stamp_dir ${assimp_build_dir}/stamp)
set(assimp_tmp_dir ${assimp_build_dir}/tmp)

set(assimp_PREFIX "${assimp_checkout_Dir}")
set(assimp_INSTALL_DIR "${assimp_build_dir}/install")

set(assimp_CMAKE_ARGS -DCMAKE_INSTALL_PREFIX=${assimp_INSTALL_DIR} -DBUILD_SHARED_LIBS=OFF  —DASSIMP_BUILD_TESTS=OFF —DASSIMP_BUILD_ASSIMP_TOOLS=OFF -DCMAKE_BUILD_TYPE=RELEASE -DASSIMP_INSTALL_PDB=OFF)
set(assimp_DIR "${assimp_INSTALL_DIR}")

if (${CMAKE_SYSTEM_NAME} MATCHES "Darwin")
	set( assimp_CMAKE_ARGS ${assimp_CMAKE_ARGS} -DCMAKE_XCODE_ATTRIBUTE_CLANG_CXX_LANGUAGE_STANDARD="c++11")
endif()

if (WIN32)
    set(glfw_CMAKE_ARGS ${assimp_CMAKE_ARGS} -DUSE_MSVC_RUNTIME_LIBRARY_DLL=ON)
endif()
   
ExternalProject_add(assimp
		PREFIX ${assimp_PREFIX}
		GIT_REPOSITORY https://github.com/assimp/assimp.git
		INSTALL_DIR ${assimp_INSTALL_DIR}
		CMAKE_ARGS ${assimp_CMAKE_ARGS}
		UPDATE_COMMAND ""
		DOWNLOAD_DIR ${assimp_checkout_Dir}
		SOURCE_DIR ${assimp_checkout_Dir}
		TMP_DIR ${assimp_tmp_dir}
		STAMP_DIR ${assimp_stamp_dir}
		BINARY_DIR ${assimp_build_dir}
)
 
#set_property(TARGET assimp PROPERTY FOLDER "Dependencies")

set(ASSIMP_INCLUDE_DIR ${assimp_INSTALL_DIR}/include CACHE INTERNAL "Directory of Assimp header files")
if(MSVC)
  # in order to prevent DLL hell, each of the DLLs have to be suffixed with the major version and msvc prefix
  if( MSVC70 OR MSVC71 )
    set(MSVC_PREFIX "vc70")
  elseif( MSVC80 )
    set(MSVC_PREFIX "vc80")
  elseif( MSVC90 )
    set(MSVC_PREFIX "vc90")
  elseif( MSVC10 )
    set(MSVC_PREFIX "vc100")
  elseif( MSVC11 )
    set(MSVC_PREFIX "vc110")
  elseif( MSVC12 )
    set(MSVC_PREFIX "vc120")
  elseif( MSVC14 )
    set(MSVC_PREFIX "vc140")
  else()
    set(MSVC_PREFIX "vc150")
  endif()
  set(ASSIMP_LIBRARY_SUFFIX "-${MSVC_PREFIX}-mt" CACHE STRING "the suffix for the assimp windows library" FORCE)
	set(ASSIMP_LIBRARY ${assimp_INSTALL_DIR}/lib/assimp${ASSIMP_LIBRARY_SUFFIX}.lib CACHE INTERNAL "Assimp lib file")
else()
	set(ASSIMP_LIBRARY ${assimp_INSTALL_DIR}/lib/libassimp.a CACHE INTERNAL "Assimp lib file")
endif()	
include_directories(${ASSIMP_INCLUDE_DIR})

find_package(ZLIB QUIET)
if (NOT ZLIB_FOUND)
	if (MSVC)
		message("Zlib not found. Setting it to zlib built with assimp")
		set(ZLIB_LIBRARY debug ${assimp_INSTALL_DIR}/lib/zlibstaticd.lib)
		set(ZLIB_LIBRARIES ${ZLIB_LIBRARY} CACHE INTERNAL "Zlib libraries")
		SET(ZLIB_FOUND 1)
	endif()
endif()