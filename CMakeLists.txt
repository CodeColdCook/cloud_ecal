# Minimum CMake required
cmake_minimum_required(VERSION 3.0)

project(cloud_protobuf)

# Find required protobuf package
find_package(Protobuf  REQUIRED)
find_package(eCAL REQUIRED)

set(PROTO_GEN_CXX 1)
set(PROTO_GEN_JAVA 0)
set(PROTO_GEN_PYTHON 0)
set(PROTO_GEN_C 0)

if(protobuf_VERBOSE)
  message(STATUS "Using Protocol Buffers ${protobuf_VERSION}")
endif()

message (STATUS "Project source dir : ${PROJECT_SOURCE_DIR}")

# Set c++ output path
SET(MESSAGE_DIR ${PROJECT_SOURCE_DIR}/output_cxx)
if(EXISTS "${MESSAGE_DIR}" AND IS_DIRECTORY "${MESSAGE_DIR}")
        SET(PROTO_META_BASE_DIR ${MESSAGE_DIR})
else()
        file(MAKE_DIRECTORY ${MESSAGE_DIR})
        SET(PROTO_META_BASE_DIR ${MESSAGE_DIR})
endif()

# Set java output path 
SET(JAVA_MESSAGE_DIR ${PROJECT_SOURCE_DIR}/output_java)
if(EXISTS "${JAVA_MESSAGE_DIR}" AND IS_DIRECTORY "${JAVA_MESSAGE_DIR}")
        SET(JAVA_PROTO_META_BASE_DIR ${JAVA_MESSAGE_DIR})
else()
        file(MAKE_DIRECTORY ${JAVA_MESSAGE_DIR})
        SET(JAVA_PROTO_META_BASE_DIR ${JAVA_MESSAGE_DIR})
endif()

# Set python output path
SET(PYTHON_MESSAGE_DIR ${PROJECT_SOURCE_DIR}/output_python)
if(EXISTS "${PYTHON_MESSAGE_DIR}" AND IS_DIRECTORY "${PYTHON_MESSAGE_DIR}")
        SET(PYTHON_PROTO_META_BASE_DIR ${PYTHON_MESSAGE_DIR})
else()
        file(MAKE_DIRECTORY ${PYTHON_MESSAGE_DIR})
        SET(PYTHON_PROTO_META_BASE_DIR ${PYTHON_MESSAGE_DIR})
endif()

# set global value
set_property(GLOBAL PROPERTY CLOUD_PROTOBUF_DIR ${MESSAGE_DIR})

# set proto file path
LIST(APPEND PROTO_FLAGS -I${PROJECT_SOURCE_DIR}/proto)
#获取需要编译的proto文件
file(GLOB_RECURSE MSG_PROTOS ${PROJECT_SOURCE_DIR}/proto/*.proto)

set(MESSAGE_SRC "")
set(MESSAGE_HDRS "")

foreach(ABS_FIL ${MSG_PROTOS})
        file(RELATIVE_PATH REL_FIL ${PROJECT_SOURCE_DIR}/proto ${ABS_FIL})
        get_filename_component(FIL_WE ${ABS_FIL} NAME_WE)
        get_filename_component(DIR ${REL_FIL} DIRECTORY)

        # message(STATUS ${ABS_FIL})
        # message(STATUS ${DIR})

        list(APPEND MESSAGE_SRC "${MESSAGE_DIR}/${DIR}/${FIL_WE}.pb.cc")
        list(APPEND MESSAGE_HDRS "${MESSAGE_DIR}/${DIR}/${FIL_WE}.pb.h")
        
        if(PROTO_GEN_CXX)
          execute_process(
            COMMAND  ${PROTOBUF_PROTOC_EXECUTABLE} ${PROTO_FLAGS} --cpp_out=${PROTO_META_BASE_DIR} ${ABS_FIL}
          )
        endif(PROTO_GEN_CXX)

        if(PROTO_GEN_JAVA)
          execute_process(
            COMMAND  ${PROTOBUF_PROTOC_EXECUTABLE} ${PROTO_FLAGS} --java_out=${JAVA_PROTO_META_BASE_DIR} ${ABS_FIL}
          )
        endif(PROTO_GEN_JAVA)

        if(PROTO_GEN_PYTHON)
        execute_process(
          COMMAND  ${PROTOBUF_PROTOC_EXECUTABLE} ${PROTO_FLAGS} --python_out=${PYTHON_PROTO_META_BASE_DIR} -I ${ABS_FIL}
        )
        endif(PROTO_GEN_PYTHON)

endforeach()

set_source_files_properties(${MESSAGE_SRC} ${MESSAGE_HDRS} PROPERTIES GENERATED TRUE)

include_directories(${MESSAGE_DIR})

add_library(${PROJECT_NAME} SHARED ${MESSAGE_SRC} ${MESSAGE_HDRS})
target_link_libraries(${PROJECT_NAME}
                    eCAL::core
                    protobuf::libprotobuf
)

