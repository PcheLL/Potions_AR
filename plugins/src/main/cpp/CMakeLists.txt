cmake_minimum_required(VERSION 3.6)

add_library(lib_iconv STATIC IMPORTED)
set_target_properties(lib_iconv PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/lib/${ANDROID_ABI}/libiconv.a)

add_library(lib_zbar STATIC IMPORTED)
set_target_properties(lib_zbar PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/lib/${ANDROID_ABI}/libzbar.a)

add_library(lib_opencv_core SHARED IMPORTED)
set_target_properties(lib_opencv_core PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/lib/${ANDROID_ABI}/libopencv_core.so)

add_library(lib_opencv_imgproc SHARED IMPORTED)
set_target_properties(lib_opencv_imgproc PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/lib/${ANDROID_ABI}/libopencv_imgproc.so)

add_library(lib_opencv_objdetect SHARED IMPORTED)
set_target_properties(lib_opencv_objdetect PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/lib/${ANDROID_ABI}/libopencv_objdetect.so)

add_library(lib_tbb SHARED IMPORTED)
set_target_properties(lib_tbb PROPERTIES IMPORTED_LOCATION ${CMAKE_SOURCE_DIR}/lib/${ANDROID_ABI}/libtbb.so)

add_library(lib_nativeSDK SHARED IMPORTED)
set_target_properties(lib_nativeSDK PROPERTIES IMPORTED_LOCATION ${WIKITUDE_NATIVE_PATH}/${ANDROID_ABI}/libnativesdk.so)

add_library(wikitudePlugins SHARED
    src/jniHelper.cpp
    src/JniRegistration.cpp
    src/barcode/BarcodePlugin.cpp
    src/face_detection/FaceDetectionPlugin.cpp
    src/face_detection/FaceDetectionPluginConnector.cpp
    src/simple_input_plugin/SimpleInputPlugin.cpp
    src/FrameInputPluginModule.cpp
    src/custom_camera/YUVFrameInputPlugin.cpp
    src/custom_camera/YUVFrameShaderSourceObject.cpp
    src/custom_camera/OpenGLESScanningEffectRenderingPluginModule.cpp
)

set_target_properties(wikitudePlugins PROPERTIES LIBRARY_OUTPUT_DIRECTORY "${PLUGIN_OUTPUT_PATH}/${ANDROID_ABI}")

target_include_directories(wikitudePlugins
    PRIVATE
    include
    include/opencv2
    include/opencv2/core
    include/opencv2/imgproc
    include/opencv2/objdetect
    include/wikitude
    include/zbar

    src
    src/barcode
    src/face_detection
    src/simple_input_plugin
    src/custom_camera
)

target_link_libraries(wikitudePlugins
    lib_nativeSDK
    lib_zbar
    lib_iconv
    lib_opencv_objdetect
    lib_opencv_imgproc
    lib_opencv_core
    lib_tbb
    log
    GLESv2
    z
)

add_custom_command(TARGET wikitudePlugins PRE_BUILD
    COMMAND "${CMAKE_COMMAND}" -E
    copy
    "${ANDROID_NDK}/sources/cxx-stl/llvm-libc++/libs/${ANDROID_ABI}/libc++_shared.so"
    "${CMAKE_SOURCE_DIR}/../jniLibs/${ANDROID_ABI}/libc++_shared.so"
)
