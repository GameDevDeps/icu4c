file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_icupkg ${ICU_TOOL_SOURCES})
set_target_properties(icu_icupkg PROPERTIES OUTPUT_NAME "icupkg")
target_include_directories(icu_icupkg PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_icupkg PRIVATE icu_libsicutu)

add_executable(ICU::icupkg ALIAS icu_icupkg)
set_target_properties(icu_icupkg PROPERTIES EXPORT_NAME "icupkg")

set_target_properties(icu_icupkg PROPERTIES BINARY_NAME "icupkg")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_icupkg PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_icupkg PROPERTIES TOOL_PLATFORM "UNIX")
    target_link_options(icu_icupkg PRIVATE "SHELL:-Wl,--no-as-needed" "SHELL:-lpthread" "SHELL:-Wl,--as-needed")
endif()
set_property(TARGET icu_icupkg APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
