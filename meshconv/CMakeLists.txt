cmake_minimum_required(VERSION 3.12)

project(meshconv)

find_package(MiniFlowVR REQUIRED)

set(SOURCE_FILES
    meshconv.cpp
    tesselate.cpp
    )

add_executable(${PROJECT_NAME} ${SOURCE_FILES})

target_link_libraries(${PROJECT_NAME} miniFlowVR)

install(TARGETS meshconv
        RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR})

