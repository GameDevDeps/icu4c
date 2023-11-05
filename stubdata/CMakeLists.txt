file(GLOB ICU_STUBDATA_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_STUBDATA_PRIVATE_DEFS "U_COMMON_IMPLEMENTATION")

add_library(icu_libsicudata STATIC ${ICU_STUBDATA_SOURCES})
set_target_properties(icu_libsicudata PROPERTIES OUTPUT_NAME "sicudata")
target_include_directories(icu_libsicudata PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/../common)
target_compile_definitions(icu_libsicudata PUBLIC "U_STATIC_IMPLEMENTATION" "U_ENABLE_DYLOAD=0")
target_compile_definitions(icu_libsicudata PRIVATE ${ICU_STUBDATA_PRIVATE_DEFS})

add_library(ICU::sicudata ALIAS icu_libsicudata)
set_target_properties(icu_libsicudata PROPERTIES EXPORT_NAME "sicudata")

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu_libicudata SHARED ${ICU_STUBDATA_SOURCES})
    set_target_properties(icu_libicudata PROPERTIES OUTPUT_NAME "icudata")
    target_include_directories(icu_libicudata PUBLIC ${CMAKE_CURRENT_SOURCE_DIR} ${CMAKE_CURRENT_SOURCE_DIR}/../common)
    target_compile_definitions(icu_libicudata PUBLIC "U_ENABLE_DYLOAD=0")
    target_compile_definitions(icu_libicudata PRIVATE ${ICU_STUBDATA_PRIVATE_DEFS})

    add_library(ICU::icudata ALIAS icu_libicudata)
    set_target_properties(icu_libicudata PROPERTIES EXPORT_NAME "icudata")
endif()
