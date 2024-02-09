cmake_minimum_required(VERSION 3.15)

project(
    ImGuiProject
)

execute_process(COMMAND git submodule update --init imgui WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
execute_process(COMMAND git submodule update --init kthook WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
execute_process(COMMAND git submodule update --init ktsignal WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/kthook)
execute_process(COMMAND git submodule update --init xbyak WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/kthook)

add_subdirectory(kthook)

aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/src ${PROJECT_NAME}_LIST)
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/imgui ${PROJECT_NAME}_LIST)
aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/kthook/hde ${PROJECT_NAME}_LIST)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/imgui)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/imgui/backends)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook/ktsignal/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook/xbyak)

list(APPEND ${PROJECT_NAME}_LIST "${CMAKE_CURRENT_SOURCE_DIR}/imgui/backends/imgui_impl_win32.cpp")
list(APPEND ${PROJECT_NAME}_LIST "${CMAKE_CURRENT_SOURCE_DIR}/imgui/backends/imgui_impl_dx9.cpp")

add_library(${PROJECT_NAME} SHARED)
target_sources(${PROJECT_NAME} PRIVATE
    ${${PROJECT_NAME}_LIST}
)

set_target_properties(${PROJECT_NAME}
	PROPERTIES
	OUTPUT_NAME ${PROJECT_NAME}
	SUFFIX ".asi"
)

if(NOT "${DXSDK_DIR}" STREQUAL "")
    target_include_directories( ${PROJECT_NAME} PUBLIC ${DXSDK_DIR}/Include)
    target_link_directories(${PROJECT_NAME} PUBLIC ${DXSDK_DIR}/Lib/x86)
elseif(NOT "$ENV{DXSDK_DIR}" STREQUAL "")
    target_include_directories( ${PROJECT_NAME} PUBLIC $ENV{DXSDK_DIR}/Include)
    target_link_directories( ${PROJECT_NAME} PUBLIC $ENV{DXSDK_DIR}/Lib/x86)
endif()

target_link_libraries(${PROJECT_NAME} d3d9 d3dx9)
target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)