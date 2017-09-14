cmake_minimum_required(VERSION 2.8.11)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_LIST_DIR}/cmake")

set(CMAKE_CXX_STANDARD 14)

if (WIN32)
    SET(CSI_INCLUDE_PATH
            ${CMAKE_SOURCE_DIR}/../include
            ${CMAKE_SOURCE_DIR}/../zlib
            ${CMAKE_SOURCE_DIR}/../zlib/build
            ${CMAKE_SOURCE_DIR}/../boost
            ${CMAKE_SOURCE_DIR}/../avro/lang/c++/include
            )
    SET(CSI_LIBRARY_PATH
            ${CMAKE_SOURCE_DIR}/../zlib/build
            ${CMAKE_SOURCE_DIR}/../boost/stage/lib/$(Platform)/lib
            ${CMAKE_SOURCE_DIR}/../avro/lang/c++/build
            )

    SET(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/lib/$(Platform))
    SET(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/bin/$(Platform))

    add_definitions(-D_FILE_OFFSET_BITS=64 -D_REENTRANT -DEXTERNAL_LOCKS -DMULTITHREAD)
    add_definitions(-DBOOST_ASIO_HAS_STD_CHRONO)
    add_definitions(-DMARCH_x86_64)
    add_definitions(-D_WIN32_WINNT=0x0602) # win8.1 to get nthll 64 bit
    add_definitions(-DWINVER=0x0601)
    add_definitions(-DCF_WINDOWS)
    add_definitions(/MP)
    add_definitions(/bigobj)
    add_definitions(-D_UNICODE)
    add_definitions(-DUNICODE)
    add_definitions(-D_CRT_SECURE_NO_WARNINGS)
    add_definitions(-D_SCL_SECURE_NO_WARNINGS)
    add_definitions(-DSECURITY_WIN32)
    add_definitions(/wd4996) # 'strdup': The POSIX name for this item is deprecated
    add_definitions(/wd4197) # 'cf_atomic32' : top-level volatile in cast is ignored
    add_definitions(/wd4200) # nonstandard extension used : zero-sized array in struct/union
    add_definitions(/wd4267) # 'argument': conversion from 'size_t' to 'int', possible loss of data  (AVRO is filled with those)
    add_definitions(/wd4090) # warning C4090: 'function': different 'const' qualifiers  (librdkafka usage of openssl)
    add_definitions(-DBOOST_ALL_STATIC_LINK)
    SET(CSI_AVRO_LIBS avrocpp_s)
    SET(ZLIB_LIBS debug zlibstaticd optimized zlibstatic)
    SET(CSI_LIBS ${CSI_AVRO_LIBS} ${CSI_BOOST_LIBS} ${ZLIB_LIBS} crypt32 Ws2_32 legacy_stdio_definitions)

elseif (APPLE)
    find_package(Boost COMPONENTS program_options filesystem system REQUIRED)
    set(BOOST_LIBS
            ${Boost_PROGRAM_OPTIONS_LIBRARY}
            ${Boost_FILESYSTEM_LIBRARY}
            ${Boost_SYSTEM_LIBRARY}
            pthread
            c
            )
    find_package(AvroCPP REQUIRED)

    SET(CSI_INCLUDE_PATH ${Boost_INCLUDE_DIR} ${AVRO_INCLUDE_DIRS})
    SET(CSI_LIBS ${AVRO_LIBRARIES} ${BOOST_LIBS})
    set(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/lib)
    SET(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/bin)
    #END APPLE
else () #LINUX
    find_package(Boost COMPONENTS program_options filesystem system REQUIRED)
    set(BOOST_LIBS
            ${Boost_PROGRAM_OPTIONS_LIBRARY}
            ${Boost_FILESYSTEM_LIBRARY}
            ${Boost_SYSTEM_LIBRARY}
            pthread
            c
            )

    find_package(AvroCPP REQUIRED)

    SET(CSI_INCLUDE_PATH ${Boost_INCLUDE_DIR} ${AVRO_INCLUDE_DIRS})
    SET(CSI_LIBS ${AVRO_LIBRARIES} ${BOOST_LIBS})

    SET(LIBRARY_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/lib)
    SET(EXECUTABLE_OUTPUT_PATH ${CMAKE_SOURCE_DIR}/bin)
    add_definitions(-D_FILE_OFFSET_BITS=64 -D_REENTRANT -DEXTERNAL_LOCKS -DMULTITHREAD)
    add_definitions(-fPIC)
    add_definitions(-Wno-deprecated)
endif () #LINUX

include_directories(${CSI_INCLUDE_PATH})
link_directories(${CSI_LIBRARY_PATH})

add_subdirectory(tools)


