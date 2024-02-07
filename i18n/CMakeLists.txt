file(GLOB ICU_I18N_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_I18N_PRIVATE_DEFS "U_I18N_IMPLEMENTATION")
if(ICU_OPT_PLATFORM_WINDOWS)
    # set minimal version to Win7 to support ResolveLocalName
    list(APPEND ICU_I18N_PRIVATE_DEFS "-DWINVER=0x0601" "-D_WIN32_WINNT=0x0601")
endif()

add_library(icu.libsicuin STATIC ${ICU_I18N_SOURCES})
set_target_properties(icu.libsicuin PROPERTIES OUTPUT_NAME "sicuin")
target_include_directories(icu.libsicuin PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
target_link_libraries(icu.libsicuin PUBLIC icu.libsicuuc)
target_compile_definitions(icu.libsicuin PRIVATE ${ICU_I18N_PRIVATE_DEFS})

add_library(ICU::sicuin ALIAS icu.libsicuin)
set_target_properties(icu.libsicuin PROPERTIES EXPORT_NAME "sicuin")

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu.libicuin SHARED ${ICU_I18N_SOURCES})
    set_target_properties(icu.libicuin PROPERTIES OUTPUT_NAME "icuin")
    target_include_directories(icu.libicuin PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu.libicuin PUBLIC icu.libicuuc)
    target_compile_definitions(icu.libicuin PRIVATE ${ICU_I18N_PRIVATE_DEFS})

    add_library(ICU::icuin ALIAS icu.libicuin)
    set_target_properties(icu.libicuin PROPERTIES EXPORT_NAME "icuin")
endif()
