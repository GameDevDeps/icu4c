file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_gendict ${ICU_TOOL_SOURCES})
set_target_properties(icu_gendict PROPERTIES OUTPUT_NAME "gendict")
target_include_directories(icu_gendict PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_gendict PRIVATE icu_libsicutu)

add_executable(ICU::gendict ALIAS icu_gendict)
set_target_properties(icu_gendict PROPERTIES EXPORT_NAME "gendict")

set_target_properties(icu_gendict PROPERTIES BINARY_NAME "gendict")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_gendict PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_gendict PROPERTIES TOOL_PLATFORM "UNIX")
    target_link_options(icu_gendict PRIVATE "SHELL:-Wl,--no-as-needed" "SHELL:-lpthread" "SHELL:-Wl,--as-needed")
endif()
set_property(TARGET icu_gendict APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
