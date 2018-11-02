project(MDig)

# Sets the minimum version of CMake required to build your native library.
# This ensures that a certain set of CMake features is available to
# your build.
cmake_minimum_required(VERSION 3.4.1)

set(CMAKE_VERBOSE_MAKEFILE on)

set(OPENCV_INCLUDE_PATH "/data/ai/workspace/OpenCV-android-sdk/sdk/native/jni/include")

get_filename_component(MDIG_SRC_DIR  ${CMAKE_SOURCE_DIR}/..  ABSOLUTE)

# shared lib will also be tucked into APK and sent to target
# refer to app/build.gradle, jniLibs section for that purpose.
# ${ANDROID_ABI} is handy for our purpose here. Probably this ${ANDROID_ABI} is
# the most valuable thing of this sample, the rest are pretty much normal cmake
add_library(libjpcnn SHARED IMPORTED)
set_target_properties(libjpcnn PROPERTIES IMPORTED_LOCATION
    ${MDIG_SRC_DIR}/jniLibs/${ANDROID_ABI}/libjpcnn.so)

add_library(libopencv SHARED IMPORTED)
set_target_properties(libopencv PROPERTIES IMPORTED_LOCATION
    ${MDIG_SRC_DIR}/jniLibs/${ANDROID_ABI}/libopencv_java.so)

# Specifies a library name, specifies whether the library is STATIC or
# SHARED, and provides relative paths to the source code. You can
# define multiple libraries by adding multiple add.library() commands,
# and CMake builds them for you. When you build your app, Gradle
# automatically packages shared libraries with your APK.

file(GLOB_RECURSE mdig_sources ${MDIG_SRC_DIR}/jni/*.*)

add_library(mdig SHARED ${mdig_sources})
target_include_directories(mdig PRIVATE
                           ${OPENCV_INCLUDE_PATH}
                          )
link_directories(mdig
                 ${MDIG_SRC_DIR}/jniLibs/armeabi-v71)
target_link_libraries(mdig
                      log
                      libjpcnn
                      libopencv
                     )
