cmake_minimum_required(VERSION 3.18)
project(gpu_delegate LANGUAGES OBJCXX CXX)

set(SOURCE_FILES
	metal_delegate.mm
	metal_delegate.h
)

set(TENSORFLOW_SOURCE_DIR "" CACHE PATH
  "Directory that contains the TensorFlow project"
)
if(NOT TENSORFLOW_SOURCE_DIR)
  get_filename_component(TENSORFLOW_SOURCE_DIR
    "${CMAKE_CURRENT_LIST_DIR}/../../../../"
    ABSOLUTE
  )
endif()
set(TF_SOURCE_DIR "${TENSORFLOW_SOURCE_DIR}/tensorflow")

# Common include directories
set(TFLITE_INCLUDE_DIRS
  "${TENSORFLOW_SOURCE_DIR}"
)
include_directories(
  BEFORE
    ${TFLITE_INCLUDE_DIRS}
)

# ../../../tools/cmake/modules/
list(APPEND CMAKE_MODULE_PATH "${TF_SOURCE_DIR}/lite/tools/cmake/modules/")
find_package(absl REQUIRED)
find_package(fp16_headers REQUIRED)
find_package(flatbuffers REQUIRED)

#list(APPEND CMAKE_FRAMEWORK_PATH "/cctools-port/usage_examples/ios_toolchain/target/SDK/iPhoneOS13.2.sdk/System/Library/Frameworks/")
list(APPEND CMAKE_FRAMEWORK_PATH "/osxcross/target/SDK/MacOSX11.1.sdk/System/Library/Frameworks/")
FIND_PATH(METAL_INCLUDE_DIR Metal/Metal.h)

set(CMAKE_XCODE_ATTRIBUTE_FRAMEWORK_SEARCH_PATHS )

include_directories(${CMAKE_BINARY_DIR}/abseil-cpp)
include_directories(${CMAKE_BINARY_DIR}/flatbuffers/include)
include_directories(${METAL_INCLUDE_DIR})
include_directories(${CMAKE_OSX_SYSROOT}/usr/include)
include_directories(${CMAKE_OSX_SYSROOT}/usr/include/c++)

add_library(gpu_delegate STATIC ${SOURCE_FILES})

# would be necessary if you run vanila clang
#target_compile_options(gpu_delegate PUBLIC -arch arm64 -target arm --target=x86_64-apple-ios12-macabib)

target_link_libraries(
	gpu_delegate 
	objc
	"-framework AppKit"
)

add_subdirectory("metal")
