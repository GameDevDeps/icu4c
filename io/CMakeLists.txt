file(GLOB ICU_IO_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_IO_PRIVATE_DEFS "U_IO_IMPLEMENTATION")

add_library(icu.libsicuio STATIC ${ICU_IO_SOURCES})
set_target_properties(icu.libsicuio PROPERTIES OUTPUT_NAME "sicuio")
target_include_directories(icu.libsicuio PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu.libsicuio PUBLIC icu.libsicuin)
target_compile_definitions(icu.libsicuio PRIVATE ${ICU_IO_PRIVATE_DEFS})

add_library(ICU::sicuio ALIAS icu.libsicuio)
set_target_properties(icu.libsicuio PROPERTIES EXPORT_NAME "sicuio")

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu.libicuio SHARED ${ICU_IO_SOURCES})
    set_target_properties(icu.libicuio PROPERTIES OUTPUT_NAME "icuio")
    target_include_directories(icu.libicuio PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu.libicuio PUBLIC icu.libicuin)
    target_compile_definitions(icu.libicuio PRIVATE ${ICU_IO_PRIVATE_DEFS})

    add_library(ICU::icuio ALIAS icu.libicuio)
    set_target_properties(icu.libicuio PROPERTIES EXPORT_NAME "icuio")
endif()
