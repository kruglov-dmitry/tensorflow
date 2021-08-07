set(SOURCE_FILES
	metal_delegate.mm
	metal_delegate.h
)

list(APPEND CMAKE_FRAMEWORK_PATH "/osxcross/target/SDK/MacOSX11.1.sdk/System/Library/Frameworks/")
FIND_PATH(METAL_INCLUDE_DIR Metal/Metal.h)

set(CMAKE_XCODE_ATTRIBUTE_FRAMEWORK_SEARCH_PATHS )

include_directories(${CMAKE_BINARY_DIR}/abseil-cpp)
include_directories(${CMAKE_BINARY_DIR}/flatbuffers/include)
include_directories(${METAL_INCLUDE_DIR})
include_directories(${CMAKE_OSX_SYSROOT}/usr/include)
include_directories(${CMAKE_OSX_SYSROOT}/usr/include/c++)

add_library(gpu_delegate STATIC ${SOURCE_FILES})

target_link_libraries(
	gpu_delegate 
	objc
	"-framework AppKit"
)

add_subdirectory("metal")
