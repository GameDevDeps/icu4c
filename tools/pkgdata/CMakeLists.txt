file(GLOB ICU_TOOL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp ${CMAKE_CURRENT_SOURCE_DIR}/*.c)

add_executable(icu_pkgdata ${ICU_TOOL_SOURCES})
set_target_properties(icu_pkgdata PROPERTIES OUTPUT_NAME "pkgdata")
target_include_directories(icu_pkgdata PRIVATE ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_pkgdata PRIVATE icu_libsicutu)

add_executable(ICU::pkgdata ALIAS icu_pkgdata)
set_target_properties(icu_pkgdata PROPERTIES EXPORT_NAME "pkgdata")

set_target_properties(icu_pkgdata PROPERTIES BINARY_NAME "pkgdata")
if(ICU_OPT_PLATFORM_WINDOWS)
    set_target_properties(icu_pkgdata PROPERTIES TOOL_PLATFORM "WIN32")
else()
    set_target_properties(icu_pkgdata PROPERTIES TOOL_PLATFORM "UNIX")
endif()
set_property(TARGET icu_pkgdata APPEND PROPERTY EXPORT_PROPERTIES TOOL_PLATFORM BINARY_NAME)
