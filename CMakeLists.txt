cmake_minimum_required(VERSION 3.25)

project(
    ImGuiProject
)

include(FetchContent)

FetchContent_Declare(
	kthook
	GIT_REPOSITORY https://github.com/kin4stat/kthook.git
	GIT_TAG 910feafb338e0605922c2aaa38dd471eabef4264
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/kthook
)
FetchContent_Declare(
	imgui
	GIT_REPOSITORY https://github.com/ocornut/imgui.git
	GIT_TAG 913151caa8905370229e361486b10c890aeaed45
    SOURCE_DIR ${CMAKE_CURRENT_SOURCE_DIR}/imgui
)
FetchContent_MakeAvailable(kthook imgui)

include_directories(${CMAKE_CURRENT_SOURCE_DIR}/imgui)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/imgui/backends)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook/ktsignal/include)
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/kthook/xbyak)

aux_source_directory(${CMAKE_CURRENT_SOURCE_DIR}/imgui ${PROJECT_NAME}_LIST)
list(APPEND ${PROJECT_NAME}_LIST "${CMAKE_CURRENT_SOURCE_DIR}/imgui/backends/imgui_impl_win32.cpp")
list(APPEND ${PROJECT_NAME}_LIST "${CMAKE_CURRENT_SOURCE_DIR}/imgui/backends/imgui_impl_dx9.cpp")

add_library(${PROJECT_NAME} SHARED)
target_sources(${PROJECT_NAME} PRIVATE
    ${${PROJECT_NAME}_LIST}
	src/main.cpp
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

target_link_libraries(${PROJECT_NAME} d3d9 d3dx9 kthook)
target_compile_features(${PROJECT_NAME} PUBLIC cxx_std_17)