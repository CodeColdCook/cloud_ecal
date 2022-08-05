# cloud_ecal
point cloud for ecal(protobuf)

## Dependence

- **eCAL** (Protobuf 3.0.0)

  ```shell
  sudo add-apt-repository ppa:ecal/ecal-latest
  sudo apt-get update
  sudo apt-get install ecal
  ```

## Usage

- **compile**

```bash
 git clone https://github.com/CodeColdCook/cloud_ecal.git
 mkdir build
 cd build
 cmake -DPROTO_GEN_CXX=ON -DPROTO_GEN_JAVA=ON -DPROTO_GEN_PYTHON ..
 make 
```

- **as subdirectory**

```cmake
# add subdirectory
## add protobuf, set the protobuf file language support you want to generate
set(PROTO_GEN_CXX OFF CACHE BOOL "enable CXX protobuf file generate") 
set(PROTO_GEN_JAVA OFF CACHE BOOL "enable JAVA protobuf file generate")
set(PROTO_GEN_PYTHON OFF CACHE BOOL "enable PYTHON protobuf file generate")
add_subdirectory(external/cloud_protobuf) 

# include cloud  protobuf dir 
get_property(CLOUD_PROTOBUF_INCLUDE_DIR GLOBAL PROPERTY "CLOUD_PROTOBUF_DIR" )
MESSAGE(STATUS "SMILES MOWER PROTOBUF_DIR :${CLOUD_PROTOBUF_INCLUDE_DIR}")
include_directories(${CLOUD_PROTOBUF_INCLUDE_DIR})

# target link libraies
add_executable(xxx xxxx.cpp)
target_link_libraries(xxx cloud_protobuf )
```

