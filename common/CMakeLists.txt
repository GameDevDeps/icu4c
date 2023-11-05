find_package(Threads)

file(GLOB ICU_COMMON_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_COMMON_PRIVATE_DEFS "U_COMMON_IMPLEMENTATION")
if(ICU_OPT_PLATFORM_WINDOWS)
    # set minimal version to Win7 to support LOCALE_ALLOW_NEUTRAL_NAMES
    list(APPEND ICU_COMMON_PRIVATE_DEFS "WINVER=0x0601" "_WIN32_WINNT=0x0601")
endif()

add_library(icu_libsicuuc STATIC ${ICU_COMMON_SOURCES})
set_target_properties(icu_libsicuuc PROPERTIES OUTPUT_NAME "sicuuc")
target_include_directories(icu_libsicuuc PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu_libsicuuc PRIVATE icu_libsicudata)
target_compile_definitions(icu_libsicuuc PUBLIC "U_STATIC_IMPLEMENTATION" "U_ENABLE_DYLOAD=0")
target_compile_definitions(icu_libsicuuc PRIVATE ${ICU_COMMON_PRIVATE_DEFS} Threads::Threads)

add_library(ICU::sicuuc ALIAS icu_libsicuuc)
set_target_properties(icu_libsicuuc PROPERTIES EXPORT_NAME "sicuuc")

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu_libicuuc SHARED ${ICU_COMMON_SOURCES})
    set_target_properties(icu_libicuuc PROPERTIES OUTPUT_NAME "icuuc")
    target_include_directories(icu_libicuuc PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu_libicuuc PRIVATE icu_libicudata)
    target_compile_definitions(icu_libicuuc PUBLIC "U_ENABLE_DYLOAD=0")
    target_compile_definitions(icu_libicuuc PRIVATE ${ICU_COMMON_PRIVATE_DEFS} Threads::Threads)

    add_library(ICU::icuuc ALIAS icu_libicuuc)
    set_target_properties(icu_libicuuc PROPERTIES EXPORT_NAME "icuuc")
endif()
