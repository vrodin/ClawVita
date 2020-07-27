cmake_minimum_required(VERSION 3.2)

if(NOT DEFINED CMAKE_TOOLCHAIN_FILE)
  if(DEFINED ENV{VITASDK})
    set(CMAKE_TOOLCHAIN_FILE "$ENV{VITASDK}/share/vita.toolchain.cmake" CACHE PATH "toolchain file")
  else()
    message(FATAL_ERROR "Please define VITASDK to point to your SDK path!")
  endif()
endif()
project(openclaw)
include("$ENV{VITASDK}/share/vita.cmake" REQUIRED)

set(VITA_APP_NAME "Red Rectangle")
set(VITA_TITLEID  "VSDK00017")
set(VITA_VERSION "01.00")

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} -std=gnu11")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -std=c++11")
set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -D__VITA__ -DMINIZ_NO_TIME")

# CMake compiled directories with dependencies
add_subdirectory(Box2D)
add_subdirectory(libwap)
add_subdirectory(./ThirdParty/Tinyxml)

# Directories with headers
include_directories(./Box2D/)
include_directories(./libwap/)
include_directories(./ThirdParty/)
include_directories(./ThirdParty/Tinyxml/)

# Directories with libs
link_directories(./libwap)
link_directories(./Box2D)
link_directories(./Build_Release)

set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ../Build_Release)

add_executable(${PROJECT_NAME} "")

add_subdirectory(OpenClaw)

target_link_libraries(${PROJECT_NAME}
  libwap
  tinyxml
  Box2D
  stdc++
  SDL2
  SDL2_mixer
  SDL2_ttf
  SDL2_image
  SDL2_gfx
  TinyGL
  vita2d
  SceDisplay_stub
  SceCtrl_stub
  SceAudio_stub
  SceSysmodule_stub
  SceGxm_stub
  SceCommonDialog_stub
  SceTouch_stub
  SceHid_stub
  SceAppMgr_stub
  png
  jpeg
  turbojpeg
  mpg123
  mikmod
  stdc++
  FLAC
  ogg
  vorbisfile
  vorbisenc
  vorbis
  freetype
  z
  m
)

vita_create_self(${PROJECT_NAME}.self ${PROJECT_NAME})
vita_create_vpk(${PROJECT_NAME}.vpk ${VITA_TITLEID} ${PROJECT_NAME}.self
  VERSION ${VITA_VERSION}
  NAME ${VITA_APP_NAME}
  FILE sce_sys/icon0.png sce_sys/icon0.png
  FILE sce_sys/livearea/contents/bg.png sce_sys/livearea/contents/bg.png
  FILE sce_sys/livearea/contents/startup.png sce_sys/livearea/contents/startup.png
  FILE sce_sys/livearea/contents/template.xml sce_sys/livearea/contents/template.xml)
