file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_gencnval ${ICU_TOOL_SOURCES})
set_target_properties(icu_gencnval PROPERTIES OUTPUT_NAME "gencnval")
target_include_directories(icu_gencnval PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_gencnval PRIVATE icu_libsicutu)

add_executable(ICU::gencnval ALIAS icu_gencnval)
set_target_properties(icu_gencnval PROPERTIES EXPORT_NAME "gencnval")

set_target_properties(icu_gencnval PROPERTIES BINARY_NAME "gencnval")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_gencnval PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_gencnval PROPERTIES TOOL_PLATFORM "UNIX")
    target_link_options(icu_gencnval PRIVATE "SHELL:-Wl,--no-as-needed" "SHELL:-lpthread" "SHELL:-Wl,--as-needed")
endif()
set_property(TARGET icu_gencnval APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
