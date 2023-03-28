cmake_minimum_required(VERSION 3.16)

project(maracatronics_proto LANGUAGES CXX)

find_package(Protobuf REQUIRED)
include_directories(${Protobuf_INCLUDE_DIRS})

set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

# Generate protos
file(GLOB PROTO_DIRS LIST_DIRECTORIES true referee services simulation)

foreach (DIR ${PROTO_DIRS})
  set(INPUT_DIRECTORY ${DIR})
  file(GLOB PROTOBUF_FILES "${INPUT_DIRECTORY}/*.proto")

  foreach (PROTOBUF_FILE ${PROTOBUF_FILES})
    get_filename_component(CURRENT_FILE ${PROTOBUF_FILE} NAME)
    execute_process(COMMAND protoc -I=${INPUT_DIRECTORY} --cpp_out=${CMAKE_CURRENT_SOURCE_DIR} ${CURRENT_FILE} WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR})
  endforeach ()

  get_filename_component(ABSOLUTE_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR} ABSOLUTE)
endforeach ()

file(GLOB PB_HEADER_FILES CONFIGURE_DEPENDS "*.pb.h")
file(GLOB PB_SOURCE_FILES CONFIGURE_DEPENDS "*.pb.cc")

add_library(${PROJECT_NAME} STATIC ${PB_HEADER_FILES} ${PB_SOURCE_FILES})
