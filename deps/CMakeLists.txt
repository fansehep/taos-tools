IF (${BUILD_TYPE} MATCHES "Debug")
    MESSAGE("will not build jansson and snappy, use system's")
ELSE ()
    OPTION(JANSSON_BUILD_DOCS "turn jansson build docs off" OFF)
    OPTION(JANSSON_INSTALL "turn jansson install off" OFF)
    ADD_SUBDIRECTORY(jansson)

    OPTION(BUILD_SHARED_LIBS "turn snappy build shared lib off" OFF)
    OPTION(SNAPPY_BUILD_TESTS "turn snappy build tests off" OFF)
    OPTION(SNAPPY_BUILD_BENCHMARKS "turn snappy build benchmarks off" OFF)
    OPTION(SNAPPY_INSTALL "turn snappy install off" OFF)
    add_compile_options(-Wno-error=sign-compare)
    if(TD_ARM_32)
        MESSAGE("-- temporary change flag for snappy")
        IF (${CMAKE_BUILD_TYPE} MATCHES "Debug")
            set (TEMP_CMAKE_CXX_FLAGS_DEBUG ${CMAKE_CXX_FLAGS_DEBUG})
            string(REPLACE "-Wno-pointer-to-int-cast" "" CMAKE_CXX_FLAGS_DEBUG ${CMAKE_CXX_FLAGS_DEBUG})
            string(REPLACE "-Wno-incompatible-pointer-types" "" CMAKE_CXX_FLAGS_DEBUG ${CMAKE_CXX_FLAGS_DEBUG})
            ADD_SUBDIRECTORY(snappy)
            set(CMAKE_CXX_FLAGS_DEBUG ${TEMP_CMAKE_CXX_FLAGS_DEBUG})
        ELSE ()
            set (TEMP_CMAKE_CXX_FLAGS_RELEASE ${CMAKE_CXX_FLAGS_RELEASE})
            string(REPLACE "-Wno-pointer-to-int-cast" "" CMAKE_CXX_FLAGS_RELEASE ${CMAKE_CXX_FLAGS_RELEASE})
            string(REPLACE "-Wno-incompatible-pointer-types" "" CMAKE_CXX_FLAGS_RELEASE ${CMAKE_CXX_FLAGS_RELEASE})
            ADD_SUBDIRECTORY(snappy)
            set(CMAKE_CXX_FLAGS_RELEASE ${TEMP_CMAKE_CXX_FLAGS_RELEASE})
        ENDIF ()
    ELSE()
        ADD_SUBDIRECTORY(snappy)
    ENDIF()
ENDIF ()

INCLUDE(ExternalProject)
ExternalProject_Add(
    apache-avro
    PREFIX ${CMAKE_CURRENT_BINARY_DIR}/avro
    SOURCE_DIR ${PROJECT_SOURCE_DIR}/deps/avro/lang/c
    CONFIGURE_COMMAND cmake -DCMAKE_INSTALL_PREFIX=${CMAKE_BINARY_DIR}/build/ -S ${PROJECT_SOURCE_DIR}/deps/avro/lang/c
)

ADD_SUBDIRECTORY(cJsonDemo)
ADD_SUBDIRECTORY(CUnit)

