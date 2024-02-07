file(GLOB ICU_COMMON_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_COMMON_PRIVATE_DEFS "U_COMMON_IMPLEMENTATION")
if(ICU_OPT_PLATFORM_WINDOWS)
    # set minimal version to Win7 to support LOCALE_ALLOW_NEUTRAL_NAMES
    list(APPEND ICU_COMMON_PRIVATE_DEFS "WINVER=0x0601" "_WIN32_WINNT=0x0601")
endif()

set(ICU_COMMON_LINK_LIBRARIES "icu.libsicudata")
find_package(Threads)
if(Threads_FOUND)
    list(APPEND ICU_COMMON_LINK_LIBRARIES Threads::Threads)
endif()

add_library(icu.libsicuuc STATIC ${ICU_COMMON_SOURCES})
set_target_properties(icu.libsicuuc PROPERTIES OUTPUT_NAME "sicuuc")
target_include_directories(icu.libsicuuc PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu.libsicuuc PRIVATE ${ICU_COMMON_LINK_LIBRARIES})
target_compile_definitions(icu.libsicuuc PUBLIC "U_STATIC_IMPLEMENTATION" "U_ENABLE_DYLOAD=0")
target_compile_definitions(icu.libsicuuc PRIVATE ${ICU_COMMON_PRIVATE_DEFS})

add_library(ICU::sicuuc ALIAS icu.libsicuuc)
set_target_properties(icu.libsicuuc PROPERTIES EXPORT_NAME "sicuuc")

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu.libicuuc SHARED ${ICU_COMMON_SOURCES})
    set_target_properties(icu.libicuuc PROPERTIES OUTPUT_NAME "icuuc")
    target_include_directories(icu.libicuuc PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu.libicuuc PRIVATE ${ICU_COMMON_LINK_LIBRARIES})
    target_compile_definitions(icu.libicuuc PUBLIC "U_ENABLE_DYLOAD=0")
    target_compile_definitions(icu.libicuuc PRIVATE ${ICU_COMMON_PRIVATE_DEFS})

    add_library(ICU::icuuc ALIAS icu.libicuuc)
    set_target_properties(icu.libicuuc PROPERTIES EXPORT_NAME "icuuc")
endif()
