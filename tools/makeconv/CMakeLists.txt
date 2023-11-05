file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_makeconv ${ICU_TOOL_SOURCES})
set_target_properties(icu_makeconv PROPERTIES OUTPUT_NAME "makeconv")
target_include_directories(icu_makeconv PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_makeconv PRIVATE icu_libsicutu)

add_executable(ICU::makeconv ALIAS icu_makeconv)
set_target_properties(icu_makeconv PROPERTIES EXPORT_NAME "makeconv")

set_target_properties(icu_makeconv PROPERTIES BINARY_NAME "makeconv")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_makeconv PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_makeconv PROPERTIES TOOL_PLATFORM "UNIX")
endif()
set_property(TARGET icu_makeconv APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
