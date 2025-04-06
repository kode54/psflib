cmake_minimum_required(VERSION 3.14)

project(
    psflib
    VERSION 0.1.0
    DESCRIPTION "PSF parser library"
    HOMEPAGE_URL "https://github.com/kode54/psflib"
    LANGUAGES C
)
set(target psflib)
add_library(${target} STATIC ${sources})

target_include_directories(${target} PUBLIC "${CMAKE_CURRENT_SOURCE_DIR}")

target_sources(${target}
    PRIVATE
        psflib.c
        psflib.h
        psf2fs.c
        psf2fs.h
)
