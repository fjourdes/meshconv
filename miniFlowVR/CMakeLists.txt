cmake_minimum_required(VERSION 3.12)

project(miniFlowVR)


set(HEADER_FILES

	include/ftl/cmdline.h 
	include/ftl/crc.h 
	include/ftl/fixed_array.h 
	include/ftl/mat.h 
	include/ftl/quat.h 
	include/ftl/rmath.h 
	include/ftl/type.h 
	include/ftl/vec.h 
	include/flowvr/render/bbox.h 
	include/flowvr/render/mesh.h 
	include/flowvr/render/mesh.inl 
	include/flowvr/render/noise.h
	)

set(SOURCE_FILES

	src/ftlm/cmdline.cpp 
	src/ftlm/crc.cpp 
	src/ftlm/mat.cpp 
	src/ftlm/quat.cpp 
	src/ftlm/type.cpp 
	src/librender/bbox.cpp 
	src/librender/mesh.cpp 
	src/librender/mesh_dist.cpp 
	src/librender/mesh_io_mesh.cpp 
	src/librender/mesh_io_obj.cpp 
	src/librender/mesh_io_off.cpp 
	src/librender/mesh_io_stl.cpp 
	src/librender/mesh_io_vtk.cpp 
	src/librender/mesh_io_lwo.cpp
	)
 
add_library(${PROJECT_NAME} STATIC ${HEADER_FILES} ${SOURCE_FILES})
target_include_directories(${PROJECT_NAME} PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}/include>)
target_include_directories(${PROJECT_NAME} PUBLIC $<BUILD_INTERFACE:${CMAKE_CURRENT_BINARY_DIR}>)
target_include_directories(${PROJECT_NAME} PUBLIC $<INSTALL_INTERFACE:include>)

# The code must be relocatable if we want to link a shared library against it
if(${CMAKE_CXX_COMPILER_ID} STREQUAL "GNU"
        OR ${CMAKE_CXX_COMPILER_ID} STREQUAL "Clang")
    target_compile_options(${PROJECT_NAME} PRIVATE "-fPIC")
endif()

#for preprocessor definition of MINI_FLOWVR  
set(MINI_FLOWVR TRUE)
configure_file( ${CMAKE_CURRENT_SOURCE_DIR}/MiniFlowVR.h.in
                ${CMAKE_CURRENT_BINARY_DIR}/MiniFlowVR.h )
install( FILES ${CMAKE_CURRENT_BINARY_DIR}/MiniFlowVR.h
        DESTINATION "${CMAKE_INSTALL_INCLUDEDIR}")

foreach ( file ${HEADER_FILES} )
    get_filename_component( dir ${file} DIRECTORY )
    # use CMAKE_INSTALL_PREFIX instead of CMAKE_INSTALL_INCLUDEDIR since
    # each file has already a parent directory named "include"
    install( FILES ${file} DESTINATION ${CMAKE_INSTALL_PREFIX}/${dir} )
endforeach()


install(TARGETS miniFlowVR
        EXPORT  MiniFlowVRTargets
        RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}"
        LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        )

include(CMakePackageConfigHelpers)

# MiniFlowVRConfigVersion.cmake file (same for build tree and intall)
# write_basic_package_version_file(${MINIFLOWVR_BUILD_CMAKE_DIR}/MiniFlowVRConfigVersion.cmake
                                 # VERSION ${MINIFLOWVR_VERSION}
                                 # COMPATIBILITY AnyNewerVersion)

# install(FILES ${MINIFLOWVR_BUILD_CMAKE_DIR}/MiniFlowVRConfigVersion.cmake
        # DESTINATION ${MINIFLOWVR_INSTALL_CMAKE_DIR})


configure_package_config_file(MiniFlowVRConfig.cmake.in
                            ${CMAKE_CURRENT_BINARY_DIR}/MiniFlowVRConfig.cmake
                            INSTALL_DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/MiniFlowVR"
)

install(FILES ${CMAKE_CURRENT_BINARY_DIR}/MiniFlowVRConfig.cmake
    DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/MiniFlowVR")

# MiniFlowVRTargets.cmake (installed)
install(EXPORT MiniFlowVRTargets
        NAMESPACE MiniFlowVR::
        DESTINATION "${CMAKE_INSTALL_LIBDIR}/cmake/MiniFlowVR")

# alias library so that it can always be consumed with the target name MiniFlowVR::miniFlowVR
add_library(MiniFlowVR::miniFlowVR ALIAS miniFlowVR)

