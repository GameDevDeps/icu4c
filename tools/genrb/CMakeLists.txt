set(ICU_TOOL_SOURCES
    "${CMAKE_CURRENT_SOURCE_DIR}/errmsg.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/filterrb.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/genrb.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/parse.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/prscmnts.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/rbutil.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/read.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/reslist.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/rle.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/ustr.c"
    "${CMAKE_CURRENT_SOURCE_DIR}/wrtjava.cpp"
    "${CMAKE_CURRENT_SOURCE_DIR}/wrtxml.cpp")

add_executable(icu_genrb ${ICU_TOOL_SOURCES})
set_target_properties(icu_genrb PROPERTIES OUTPUT_NAME "genrb")
target_include_directories(icu_genrb PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_genrb PRIVATE icu_libsicutu)

add_executable(ICU::genrb ALIAS icu_genrb)
set_target_properties(icu_genrb PROPERTIES EXPORT_NAME "genrb")

set_target_properties(icu_genrb PROPERTIES BINARY_NAME "genrb")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_genrb PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_genrb PROPERTIES TOOL_PLATFORM "UNIX")
endif()
set_property(TARGET icu_genrb APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
