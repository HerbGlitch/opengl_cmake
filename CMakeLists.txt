cmake_minimum_required(VERSION 3.10)
project(opengl_cmake)

add_executable(${PROJECT_NAME}
    external/imgui/imgui_demo.cpp
    external/imgui/imgui_draw.cpp
    external/imgui/imgui_tables.cpp
    external/imgui/imgui_widgets.cpp
    external/imgui/imgui.cpp
    external/imgui/backends/imgui_impl_glfw.cpp
    external/imgui/backends/imgui_impl_opengl3.cpp

    src/main.cpp
    src/Shader.cpp
)

find_package(OpenGL REQUIRED)
if(NOT OPENGL_FOUND)
    message("ERROR: OpenGL not found")
endif(NOT OPENGL_FOUND)

set (CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
set (CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})

find_package(Git QUIET)
if(GIT_FOUND AND EXISTS "${PROJECT_SOURCE_DIR}/.git")
    option(GIT_SUBMODULE "Check submodules during build" ON)
    if(GIT_SUBMODULE)
        message(STATUS "Submodule update")
        execute_process(
            COMMAND ${GIT_EXECUTABLE} 
            submodule update --init --recursive
            WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
            RESULT_VARIABLE GIT_SUBMOD_RESULT
        )
        if(NOT GIT_SUBMOD_RESULT EQUAL "0")
            message(FATAL_ERROR "git submodule update --init failed with ${GIT_SUBMOD_RESULT}")
        endif()
    endif()
endif()

add_subdirectory(external/glew)
add_subdirectory(external/assimp)
add_subdirectory(external/glfw)
add_subdirectory(external/glm)

target_include_directories(${PROJECT_NAME} 
    PUBLIC external/assimp
    PUBLIC external/glfw/inlcude
    PUBLIC external/glm
    PUBLIC external/imgui
    PUBLIC external/glew/include
)

target_link_directories(${PROJECT_NAME}
    PUBLIC external/assimp
    PUBLIC external/glfw
    PUBLIC external/glm
    PUBLIC external/imgui
    PUBLIC external/glew
)

file(COPY res DESTINATION ${CMAKE_BINARY_DIR})
target_link_libraries(${PROJECT_NAME} assimp libglew_shared glfw glm)
