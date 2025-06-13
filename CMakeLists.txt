cmake_minimum_required(VERSION 3.25)

project(MControlCenter VERSION 0.5.1 LANGUAGES CXX)

set(CMAKE_INCLUDE_CURRENT_DIR ON)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

add_compile_options(-fPIC)

find_package(Qt6 6.4 REQUIRED COMPONENTS Widgets LinguistTools DBus)

set(PROJECT_SOURCES
    src/main.cpp
    src/mainwindow.ui
    src/mainwindow.cpp src/mainwindow.h
    src/operate.cpp src/operate.h
    src/helper.cpp src/helper.h
    src/msi-ec_helper.cpp src/msi-ec_helper.h
    src/settings.cpp src/settings.h
    src/resources.qrc
)

set(TS_FILES
    MControlCenter_de_DE.ts
    MControlCenter_en.ts
    MControlCenter_es.ts
    MControlCenter_eu.ts
    MControlCenter_fr.ts
    MControlCenter_hu.ts
    MControlCenter_it.ts
    MControlCenter_nb.ts
    MControlCenter_nl_NL.ts
    MControlCenter_pt_BR.ts
    MControlCenter_pt_PT.ts
    MControlCenter_ru.ts
    MControlCenter_tr.ts
    MControlCenter_vi.ts
    MControlCenter_zh_CN.ts
)

find_program(LUPDATE_EXECUTABLE lupdate lupdate6 lupdate-qt6 PATHS /lib/qt6/bin/ REQUIRED)
find_program(LRELEASE_EXECUTABLE lrelease lrelease6 lrelease-qt6 PATHS /lib/qt6/bin/ REQUIRED)

foreach(_ts_file ${TS_FILES})
    execute_process(COMMAND
        ${LUPDATE_EXECUTABLE}
        ${CMAKE_SOURCE_DIR}
        -ts ${CMAKE_SOURCE_DIR}/src/i18n/${_ts_file}
        -source-language en_US -no-obsolete -locations none
        COMMAND_ERROR_IS_FATAL ANY
    )
    execute_process(COMMAND
        ${LRELEASE_EXECUTABLE}
        ${CMAKE_SOURCE_DIR}/src/i18n/${_ts_file}
        COMMAND_ERROR_IS_FATAL ANY
    )
endforeach()
if(Qt6_FOUND)
    qt_add_executable(mcontrolcenter
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
    )
endif()

target_link_libraries(mcontrolcenter PRIVATE Qt${QT_VERSION_MAJOR}::Widgets Qt${QT_VERSION_MAJOR}::DBus)

install(TARGETS mcontrolcenter
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR})

add_subdirectory(src/helper ${CMAKE_BINARY_DIR}/helper)
add_definitions(-DMControlCenter_VERSION="${PROJECT_VERSION}")
