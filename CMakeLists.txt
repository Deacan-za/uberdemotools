cmake_minimum_required(VERSION 3.20)
project(UDT CXX)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_POSITION_INDEPENDENT_CODE ON)

# Source layout and build settings mirror UDT_DLL/premake/premake5.lua
# (project "UDT", kind SharedLib): src/*.cpp non-recursive, include dirs
# src + src/apps + include, define UDT_CREATE_DLL.
file(GLOB UDT_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/UDT_DLL/src/*.cpp)

add_library(UDT SHARED ${UDT_SOURCES})
target_include_directories(UDT
  PUBLIC ${CMAKE_CURRENT_SOURCE_DIR}/UDT_DLL/include
  PRIVATE ${CMAKE_CURRENT_SOURCE_DIR}/UDT_DLL/src
          ${CMAKE_CURRENT_SOURCE_DIR}/UDT_DLL/src/apps)
target_compile_definitions(UDT PRIVATE UDT_CREATE_DLL)

if(MSVC)
  target_compile_definitions(UDT PRIVATE WIN32 _CRT_SECURE_NO_WARNINGS)
  target_link_libraries(UDT PRIVATE Winmm)
  target_compile_options(UDT PRIVATE /GR-)
else()
  target_compile_options(UDT PRIVATE -Wno-invalid-offsetof -Wno-narrowing
                                     -fno-rtti -fno-exceptions)
  target_link_libraries(UDT PRIVATE pthread rt)
endif()
