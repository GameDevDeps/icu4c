file(GLOB ICU_TOOLUTIL_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_TOOLUTIL_PUBLIC_DEFS "U_DISABLE_OBJ_CODE")
set(ICU_TOOLUTIL_PRIVATE_DEFS "U_TOOLUTIL_IMPLEMENTATION")

add_library(icu.libsicutu STATIC ${ICU_TOOLUTIL_SOURCES})
set_target_properties(icu.libsicutu PROPERTIES OUTPUT_NAME "sicutu")
target_include_directories(icu.libsicutu PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu.libsicutu PUBLIC icu.libsicuin icu.libsicuio)
target_compile_definitions(icu.libsicutu PUBLIC ${ICU_TOOLUTIL_PUBLIC_DEFS})
target_compile_definitions(icu.libsicutu PRIVATE ${ICU_TOOLUTIL_PRIVATE_DEFS})

add_library(ICU::sicutu ALIAS icu.libsicutu)
set_target_properties(icu.libsicutu PROPERTIES EXPORT_NAME "sicutu")

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu.libicutu SHARED ${ICU_TOOLUTIL_SOURCES})
    set_target_properties(icu.libicutu PROPERTIES OUTPUT_NAME "icutu")
    target_include_directories(icu.libicutu PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu.libicutu PUBLIC icu.libicuin icu.libicuio)
    target_compile_definitions(icu.libicutu PUBLIC ${ICU_TOOLUTIL_PUBLIC_DEFS})
    target_compile_definitions(icu.libicutu PRIVATE ${ICU_TOOLUTIL_PRIVATE_DEFS})

    add_library(ICU::icutu ALIAS icu.libicutu)
    set_target_properties(icu.libicutu PROPERTIES EXPORT_NAME "icutu")
endif()
