file(GLOB ICU_IO_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_IO_PRIVATE_DEFS "U_IO_IMPLEMENTATION")

add_library(icu_libsicuio STATIC ${ICU_IO_SOURCES})
set_target_properties(icu_libsicuio PROPERTIES OUTPUT_NAME "sicuio")
target_include_directories(icu_libsicuio PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_libsicuio PUBLIC icu_libsicuin)
target_compile_definitions(icu_libsicuio PRIVATE ${ICU_IO_PRIVATE_DEFS})

add_library(ICU::sicuio ALIAS icu_libsicuio)
set_target_properties(icu_libsicuio PROPERTIES EXPORT_NAME "sicuio")

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu_libicuio SHARED ${ICU_IO_SOURCES})
    set_target_properties(icu_libicuio PROPERTIES OUTPUT_NAME "icuio")
    target_include_directories(icu_libicuio PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu_libicuio PUBLIC icu_libicuin)
    target_compile_definitions(icu_libicuio PRIVATE ${ICU_IO_PRIVATE_DEFS})

    add_library(ICU::icuio ALIAS icu_libicuio)
    set_target_properties(icu_libicuio PROPERTIES EXPORT_NAME "icuio")
endif()
