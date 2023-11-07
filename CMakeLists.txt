cmake_minimum_required(VERSION 3.19)
project(ICU C CXX)

option(ICU_OPT_ENABLE_SHARED "Enable dynamic library build" OFF)

if(CMAKE_CROSSCOMPILING)
    if(${CMAKE_HOST_SYSTEM_NAME} MATCHES "Windows")
        set(ICU_OPT_PLATFORM_WINDOWS ON)
    endif()
else()
    if(WIN32)
        set(ICU_OPT_PLATFORM_WINDOWS ON)
    endif()
endif()

if(MSVC)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} /utf-8")
    set(CMAKE_C_FLAGS "${CMAKE_C_FLAGS} /utf-8")
endif()

# Core libraries
add_subdirectory(stubdata)
add_subdirectory(common)
add_subdirectory(i18n)
add_subdirectory(io)

# Tools
if(CMAKE_CROSSCOMPILING)
    if(ICU_OPT_HOST_BUILD_COMPLETED)
        message(STATUS "[CrossCompiling] Host build cached")
    else()
        message(STATUS "[CrossCompiling] Start host build")

        set(ICU_OPT_HOST_BUILD_WORKDIR "${CMAKE_CURRENT_BINARY_DIR}/HostBuild/")
        execute_process(COMMAND ${CMAKE_COMMAND} -E make_directory "${ICU_OPT_HOST_BUILD_WORKDIR}")

        # run host cmake
        execute_process(COMMAND "${CMAKE_COMMAND}"
            "-E" "env" "CC=" "CXX=" "ASM=" "SYSROOT="  # using host default compiler, FIXME: customize?
            "${CMAKE_COMMAND}"
            "-DCMAKE_BUILD_TYPE=RelWithDebInfo"
            "${CMAKE_CURRENT_SOURCE_DIR}"
            WORKING_DIRECTORY "${ICU_OPT_HOST_BUILD_WORKDIR}"
            RESULT_VARIABLE ICU_OPT_HOST_BUILD_RESULT)
        if(ICU_OPT_HOST_BUILD_RESULT AND NOT ICU_OPT_HOST_BUILD_RESULT EQUAL 0)
            message(FATAL_ERROR "[CrossCompiling] Generate host build cmake failed")
        endif()

        # build
        execute_process(COMMAND ${CMAKE_COMMAND}
            --build . -j
            WORKING_DIRECTORY "${ICU_OPT_HOST_BUILD_WORKDIR}"
            RESULT_VARIABLE ICU_OPT_HOST_BUILD_COMPILE_RESULT)
        if(ICU_OPT_HOST_BUILD_COMPILE_RESULT AND NOT ICU_OPT_HOST_BUILD_COMPILE_RESULT EQUAL 0)
            message(FATAL_ERROR "[CrossCompiling] Host build failed")
        endif()

        # Cache build info
        set(ICU_OPT_HOST_BUILD_COMPLETED ON CACHE INTERNAL "")
        set(ICU_OPT_HOST_BUILD_WORKDIR "${ICU_OPT_HOST_BUILD_WORKDIR}" CACHE INTERNAL "")
    endif()

    # Set native tools search directory
    set(ICUHostTools_DIR "${ICU_OPT_HOST_BUILD_WORKDIR}")

    # https://cmake.org/cmake/help/book/mastering-cmake/chapter/Cross%20Compiling%20With%20CMake.html
    find_package(ICUHostTools)
    if(NOT ICUHostTools_FOUND)
        message(FATAL_ERROR "Host building tool is required, specify -DICUHostTools_DIR to find ICUHostToolsConfig.cmake")
    endif()
else()
    add_subdirectory(tools/toolutil)

    add_subdirectory(tools/gencnval)
    add_subdirectory(tools/gencfu)
    add_subdirectory(tools/makeconv)
    add_subdirectory(tools/genbrk)
    add_subdirectory(tools/gensprep)
    add_subdirectory(tools/gendict)
    add_subdirectory(tools/icupkg)
    add_subdirectory(tools/genrb)
    add_subdirectory(tools/pkgdata)

    export(
        TARGETS
            icu_gencnval
            icu_gencfu
            icu_makeconv
            icu_genbrk
            icu_gensprep
            icu_gendict
            icu_icupkg
            icu_genrb
            icu_pkgdata
        NAMESPACE
            ICU::
        FILE
            "${CMAKE_BINARY_DIR}/ICUHostToolsConfig.cmake")
endif()

# Create preparing targets, for copying all the binaries together
set(_ICU_PREPARE_TOOL_DIR "${CMAKE_CURRENT_BINARY_DIR}/icutools/")

function(icu_prepare_tool)
    set(ONE_VALUE_ARGS TARGET IMPORT_TARGET GEN_MODE_OUT)
    cmake_parse_arguments(ICU_PREPARE_TOOL "" "${ONE_VALUE_ARGS}" "" ${ARGN})

    if(NOT TARGET ${ICU_PREPARE_TOOL_IMPORT_TARGET})
        message(FATAL_ERROR "${ICU_PREPARE_TOOL_IMPORT_TARGET} is not considered a target")
    endif()

    get_target_property(ICU_PREPARE_TOOL_PLATFORM ${ICU_PREPARE_TOOL_IMPORT_TARGET} TOOL_PLATFORM)
    get_target_property(ICU_PREPARE_TOOL_NAME ${ICU_PREPARE_TOOL_IMPORT_TARGET} BINARY_NAME)

    # 构建后移动到固定目录
    if(ICU_PREPARE_TOOL_PLATFORM STREQUAL "WIN32")
        set(${ICU_PREPARE_TOOL_GEN_MODE_OUT} "windows-exec" PARENT_SCOPE)
        add_custom_command(
            OUTPUT "${_ICU_PREPARE_TOOL_DIR}${ICU_PREPARE_TOOL_NAME}/${ICU_PREPARE_TOOL_NAME}.exe"
            COMMAND
                "${CMAKE_COMMAND}" -E copy_if_different
                "$<TARGET_FILE:${ICU_PREPARE_TOOL_IMPORT_TARGET}>"
                "${_ICU_PREPARE_TOOL_DIR}${ICU_PREPARE_TOOL_NAME}/${ICU_PREPARE_TOOL_NAME}.exe"
            DEPENDS
                ${ICU_PREPARE_TOOL_IMPORT_TARGET}
            VERBATIM)
        add_custom_target(${ICU_PREPARE_TOOL_TARGET}
            DEPENDS "${_ICU_PREPARE_TOOL_DIR}${ICU_PREPARE_TOOL_NAME}/${ICU_PREPARE_TOOL_NAME}.exe")
    else()
        set(${ICU_PREPARE_TOOL_GEN_MODE_OUT} "unix-exec" PARENT_SCOPE)
        add_custom_command(
            OUTPUT "${_ICU_PREPARE_TOOL_DIR}${ICU_PREPARE_TOOL_NAME}"
            COMMAND
                "${CMAKE_COMMAND}" -E copy_if_different
                "$<TARGET_FILE:${ICU_PREPARE_TOOL_IMPORT_TARGET}>"
                "${_ICU_PREPARE_TOOL_DIR}${ICU_PREPARE_TOOL_NAME}"
            DEPENDS
                ${ICU_PREPARE_TOOL_IMPORT_TARGET}
            VERBATIM)
        add_custom_target(${ICU_PREPARE_TOOL_TARGET}
            DEPENDS "${_ICU_PREPARE_TOOL_DIR}${ICU_PREPARE_TOOL_NAME}")
    endif()
endfunction()

icu_prepare_tool(TARGET icu_prepare_gencnval IMPORT_TARGET ICU::gencnval GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_gencfu   IMPORT_TARGET ICU::gencfu   GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_makeconv IMPORT_TARGET ICU::makeconv GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_genbrk   IMPORT_TARGET ICU::genbrk   GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_gensprep IMPORT_TARGET ICU::gensprep GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_gendict  IMPORT_TARGET ICU::gendict  GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_icupkg   IMPORT_TARGET ICU::icupkg   GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_genrb    IMPORT_TARGET ICU::genrb    GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)
icu_prepare_tool(TARGET icu_prepare_pkgdata  IMPORT_TARGET ICU::pkgdata  GEN_MODE_OUT _ICU_OPT_TOOL_GEN_MODE)

# function for generating data file

# icu_generate_data
#   Generating the ICU data file.
#   To load data file, see udata_setAppData for more details.
# Arguments:
#   TARGET        target name
#   WORKING_DIR   working directory
#   OUTPUT_DIR    specify the output directory of the data file
#   FILENAME_OUT  output variable to storage the name of the data file
#   FILTER        glob filter for matching which files should be packaged into data file, e.g. brkitr/*
# Example:
#   icu_generate_data(TARGET icu_data WORKING_DIR ${CMAKE_CURRENT_BINARY_DIR}/icudata
#       OUTPUT_DIR ${CMAKE_CURRENT_BINARY_DIR} FILENAME_OUT ICU_DATA_FILENAME FILTER "*")
function(icu_generate_data)
    set(ONE_VALUE_ARGS TARGET WORKING_DIR OUTPUT_DIR FILENAME_OUT)
    set(MULTI_VALUE_ARGS FILTER)
    cmake_parse_arguments(ICU_GENERATE_DATA "" "${ONE_VALUE_ARGS}" "${MULTI_VALUE_ARGS}" ${ARGN})

    find_package(Python3 COMPONENTS Interpreter)
    if(NOT Python3_Interpreter_FOUND)
        message(FATAL_ERROR "Python3 is required to build data")
    endif()

    # determine version
    file(READ "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/common/unicode/uvernum.h" ICU_OPT_VER_NUM_FILE_CONTENT)
    string(REGEX MATCH "U_ICU_VERSION_MAJOR_NUM ([0-9]*)" _ ${ICU_OPT_VER_NUM_FILE_CONTENT})
    set(ICU_OPT_VERSION_MAJOR ${CMAKE_MATCH_1})

    # output filename
    set(ICU_OPT_DATA_NAME "icudt${ICU_OPT_VERSION_MAJOR}")
    if("${CMAKE_CXX_BYTE_ORDER}" STREQUAL "BIG_ENDIAN")
        set(ICU_OPT_DATA_ENDIAN_SUFFIX "b")
    else()
        set(ICU_OPT_DATA_ENDIAN_SUFFIX "l")
    endif()
    set(ICU_OPT_DATA_NAME_FULL "${ICU_OPT_DATA_NAME}${ICU_OPT_DATA_ENDIAN_SUFFIX}")

    # source data
    set(ICU_OPT_DATA_SOURCE_DIR "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/data")

    # temporary directory for storing data files
    add_custom_command(
        OUTPUT "${ICU_GENERATE_DATA_WORKING_DIR}"
        COMMAND ${CMAKE_COMMAND} -E make_directory "${ICU_GENERATE_DATA_WORKING_DIR}")

    # create target
    add_custom_command(
        OUTPUT "${ICU_GENERATE_DATA_OUTPUT_DIR}/${ICU_OPT_DATA_NAME_FULL}.dat"
        WORKING_DIRECTORY "${ICU_GENERATE_DATA_WORKING_DIR}"
        COMMENT "Running icu data builder"
        COMMAND
            ${CMAKE_COMMAND} -E env "PYTHONPATH=${CMAKE_CURRENT_FUNCTION_LIST_DIR}/python" ${Python3_EXECUTABLE} -B -m icutools.databuilder
            --mode ${_ICU_OPT_TOOL_GEN_MODE}
            --src_dir "${ICU_OPT_DATA_SOURCE_DIR}"
            --tool_dir "${_ICU_PREPARE_TOOL_DIR}"
            --tool_cfg ""
            --out_dir "${ICU_GENERATE_DATA_WORKING_DIR}/${ICU_OPT_DATA_NAME_FULL}"
            --tmp_dir "${ICU_GENERATE_DATA_WORKING_DIR}/tmp"
        COMMAND
            ${Python3_EXECUTABLE} "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/python/gen_dep_list.py"
            -o "${ICU_GENERATE_DATA_WORKING_DIR}/pkg_file_list.txt"
            -s "${ICU_GENERATE_DATA_WORKING_DIR}/${ICU_OPT_DATA_NAME_FULL}"
            ${ICU_GENERATE_DATA_FILTER}
        COMMAND
            "$<TARGET_FILE:ICU::pkgdata>"
            -m common
            -p ${ICU_OPT_DATA_NAME_FULL}
            -s "${ICU_GENERATE_DATA_WORKING_DIR}/${ICU_OPT_DATA_NAME_FULL}"
            "${ICU_GENERATE_DATA_WORKING_DIR}/pkg_file_list.txt"
        COMMAND
            "${CMAKE_COMMAND}" -E copy_if_different
            "${ICU_GENERATE_DATA_WORKING_DIR}/${ICU_OPT_DATA_NAME_FULL}.dat"
            "${ICU_GENERATE_DATA_OUTPUT_DIR}/${ICU_OPT_DATA_NAME_FULL}.dat"
        DEPENDS
            "${ICU_GENERATE_DATA_WORKING_DIR}"
            "${CMAKE_CURRENT_FUNCTION_LIST_DIR}/python/gen_dep_list.py"
            icu_prepare_gencnval
            icu_prepare_gencfu
            icu_prepare_makeconv
            icu_prepare_genbrk
            icu_prepare_gensprep
            icu_prepare_gendict
            icu_prepare_icupkg
            icu_prepare_genrb
            icu_prepare_pkgdata
        VERBATIM)
    add_custom_target(${ICU_GENERATE_DATA_TARGET}
        DEPENDS "${ICU_GENERATE_DATA_OUTPUT_DIR}/${ICU_OPT_DATA_NAME_FULL}.dat")
    set(${ICU_GENERATE_DATA_FILENAME_OUT} "${ICU_OPT_DATA_NAME_FULL}.dat" PARENT_SCOPE)
endfunction()
