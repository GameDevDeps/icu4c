file(GLOB ICU_I18N_SOURCES ${CMAKE_CURRENT_SOURCE_DIR}/*.cpp)

set(ICU_I18N_PRIVATE_DEFS "U_I18N_IMPLEMENTATION")
if(ICU_OPT_PLATFORM_WINDOWS)
    # set minimal version to Win7 to support ResolveLocalName
    list(APPEND ICU_I18N_PRIVATE_DEFS "-DWINVER=0x0601" "-D_WIN32_WINNT=0x0601")
endif()

if(ICU_OPT_ENABLE_STATIC)
    add_library(icu_libsicuin STATIC ${ICU_I18N_SOURCES})
    set_target_properties(icu_libsicuin PROPERTIES OUTPUT_NAME "sicuin")
    target_include_directories(icu_libsicuin PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu_libsicuin PUBLIC icu_libsicuuc)
    target_compile_definitions(icu_libsicuin PRIVATE ${ICU_I18N_PRIVATE_DEFS})

    add_library(ICU::sicuin ALIAS icu_libsicuin)
    set_target_properties(icu_libsicuin PROPERTIES EXPORT_NAME sicuin)
endif()

if(ICU_OPT_ENABLE_SHARED)
    add_library(icu_libicuin SHARED ${ICU_I18N_SOURCES})
    set_target_properties(icu_libicuin PROPERTIES OUTPUT_NAME "icuin")
    target_include_directories(icu_libicuin PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})
    target_link_libraries(icu_libicuin PUBLIC icu_libicuuc)
    target_compile_definitions(icu_libicuin PRIVATE ${ICU_I18N_PRIVATE_DEFS})

    add_library(ICU::icuin ALIAS icu_libicuin)
    set_target_properties(icu_libicuin PROPERTIES EXPORT_NAME icuin)
endif()
