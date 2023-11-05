file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_gensprep ${ICU_TOOL_SOURCES})
set_target_properties(icu_gensprep PROPERTIES OUTPUT_NAME "gensprep")
target_include_directories(icu_gensprep PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_gensprep PRIVATE icu_libsicutu)

add_executable(ICU::gensprep ALIAS icu_gensprep)
set_target_properties(icu_gensprep PROPERTIES EXPORT_NAME "gensprep")

set_target_properties(icu_gensprep PROPERTIES BINARY_NAME "gensprep")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_gensprep PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_gensprep PROPERTIES TOOL_PLATFORM "UNIX")
endif()
set_property(TARGET icu_gensprep APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
