file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_gencfu ${ICU_TOOL_SOURCES})
set_target_properties(icu_gencfu PROPERTIES OUTPUT_NAME "gencfu")
target_include_directories(icu_gencfu PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_gencfu PRIVATE icu_libsicutu)

add_executable(ICU::gencfu ALIAS icu_gencfu)
set_target_properties(icu_gencfu PROPERTIES EXPORT_NAME "gencfu")

set_target_properties(icu_gencfu PROPERTIES BINARY_NAME "gencfu")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_gencfu PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_gencfu PROPERTIES TOOL_PLATFORM "UNIX")
endif()
set_property(TARGET icu_gencfu APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
