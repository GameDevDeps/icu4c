file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_genbrk ${ICU_TOOL_SOURCES})
set_target_properties(icu_genbrk PROPERTIES OUTPUT_NAME "genbrk")
target_include_directories(icu_genbrk PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_genbrk PRIVATE icu_libsicutu)

add_executable(ICU::genbrk ALIAS icu_genbrk)
set_target_properties(icu_genbrk PROPERTIES EXPORT_NAME "genbrk")

set_target_properties(icu_genbrk PROPERTIES BINARY_NAME "genbrk")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_genbrk PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_genbrk PROPERTIES TOOL_PLATFORM "UNIX")
endif()
set_property(TARGET icu_genbrk APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
