file(GLOB ICU_TOOLUTIL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_TOOLUTIL_PRIVATE_DEFS "U_TOOLUTIL_IMPLEMENTATION")

if(ICU_OPT_ENABLE_STATIC)
    add_library(icu_libsicutu STATIC ${ICU_TOOLUTIL_SOURCES})
    set_target_properties(icu_libsicutu PROPERTIES OUTPUT_NAME "sicutu")
    target_include_directories(icu_libsicutu PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu_libsicutu PUBLIC icu_libsicuin icu_libsicuio)
    target_compile_definitions(icu_libsicutu PRIVATE ${ICU_TOOLUTIL_PRIVATE_DEFS})

    add_library(ICU::sicutu ALIAS icu_libsicutu)
    set_target_properties(icu_libsicutu PROPERTIES EXPORT_NAME sicutu)
endif()

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu_libicutu SHARED ${ICU_TOOLUTIL_SOURCES})
    set_target_properties(icu_libicutu PROPERTIES OUTPUT_NAME "icutu")
    target_include_directories(icu_libicutu PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu_libicutu PUBLIC icu_libicuin icu_libicuio)
    target_compile_definitions(icu_libicutu PRIVATE ${ICU_TOOLUTIL_PRIVATE_DEFS})

    add_library(ICU::icutu ALIAS icu_libicutu)
    set_target_properties(icu_libicutu PROPERTIES EXPORT_NAME icutu)
endif()
