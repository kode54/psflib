cmake_minimum_required(VERSION 3.25)

option(USE_STATIC_MSVC_RUNTIME "Use static MSVC runtime (/MT)" ON)

if(USE_STATIC_MSVC_RUNTIME AND NOT DEFINED CMAKE_MSVC_RUNTIME_LIBRARY)
    set(CMAKE_MSVC_RUNTIME_LIBRARY "MultiThreaded$<$<CONFIG:Debug>:Debug>")
endif()

project(psflib LANGUAGES C)

option(PSFLIB_SHARED_OBJECT "Use Shared Object" OFF)
option(PSFLIB_EXTERNAL_ZLIB "Use external zlib" OFF)

set(ZLIB_BUILD_EXAMPLES OFF CACHE BOOL "" FORCE)
set(ZLIB_BUILD_TESTING OFF CACHE BOOL "" FORCE)

if(PSFLIB_EXTERNAL_ZLIB)
    find_package(ZLIB REQUIRED)
else()
    set(ZLIB_BUILD_SHARED OFF CACHE BOOL "" FORCE)
    set(ZLIB_BUILD_STATIC ON CACHE BOOL "" FORCE)

    include(FetchContent)

    FetchContent_Declare(
        zlib
        GIT_REPOSITORY https://github.com/madler/zlib
        GIT_TAG        v1.3.2
    )
    FetchContent_MakeAvailable(zlib)

    add_library(ZLIB::ZLIB ALIAS zlibstatic)
endif()

if(PSFLIB_SHARED_OBJECT)
    add_library(psflib OBJECT)
else()
    add_library(psflib)
endif()

target_sources(psflib
    PRIVATE
        src/psf2fs.c
        src/psflib.c
)

target_link_libraries(psflib PRIVATE ZLIB::ZLIB)

target_include_directories(psflib
    PUBLIC
        "${CMAKE_CURRENT_SOURCE_DIR}/include"
    PRIVATE
        ${zlib_SOURCE_DIR}
        ${zlib_BINARY_DIR}
)

target_compile_definitions(psflib
    PRIVATE
        NDEBUG
        _LIB
)

if(WIN32)
    target_compile_definitions(psflib
        PRIVATE
            _WIN32_WINNT=0x501
            WIN32
            UNICODE
            _UNICODE
    )
endif()

if(MSVC)
    target_compile_options(psflib
        PRIVATE
            /W3
    )
endif()

