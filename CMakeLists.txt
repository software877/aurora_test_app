################################################################################
##
## Copyright (C) 2022 ru.auroraos
## 
## This file is part of the Моё приложение для ОС Аврора project.
##
## Redistribution and use in source and binary forms,
## with or without modification, are permitted provided
## that the following conditions are met:
##
## * Redistributions of source code must retain the above copyright notice,
##   this list of conditions and the following disclaimer.
## * Redistributions in binary form must reproduce the above copyright notice,
##   this list of conditions and the following disclaimer
##   in the documentation and/or other materials provided with the distribution.
## * Neither the name of the copyright holder nor the names of its contributors
##   may be used to endorse or promote products derived from this software
##   without specific prior written permission.
##
## THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
## AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO,
## THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
## FOR A PARTICULAR PURPOSE ARE DISCLAIMED.
## IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE
## FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY,
## OR CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO,
## PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES;
## LOSS OF USE, DATA, OR PROFITS;
## OR BUSINESS INTERRUPTION)
## HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY,
## WHETHER IN CONTRACT, STRICT LIABILITY, OR TORT
## (INCLUDING NEGLIGENCE OR OTHERWISE)
## ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE,
## EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.
##
################################################################################

project(ru.auroraos.aurora_test_app CXX)
cmake_minimum_required(VERSION 3.5)

find_package (Qt5 COMPONENTS Core Network Qml Gui Quick LinguistTools REQUIRED)

include(FindPkgConfig)
pkg_search_module(AURORA auroraapp REQUIRED)
pkg_search_module(AURORA auroraapp_i18n REQUIRED)

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)


set(short_name aurora_test_app)

FILE(GLOB TsFiles "translations/*.ts")
qt5_add_translation(QmFiles ${TsFiles})

add_executable(ru.auroraos.aurora_test_app src/main.cpp ${QmFiles})
target_compile_definitions(ru.auroraos.aurora_test_app PRIVATE
    $<$<OR:$<CONFIG:Debug>,$<CONFIG:RelWithDebInfo>>:QT_QML_DEBUG>
)
target_include_directories(ru.auroraos.aurora_test_app PRIVATE
    $<BUILD_INTERFACE:
    ${AURORA_INCLUDE_DIRS}
>)
target_link_libraries(ru.auroraos.aurora_test_app
    Qt5::Quick
    ${AURORA_LDFLAGS}
)

install(TARGETS ru.auroraos.aurora_test_app
    RUNTIME DESTINATION bin
)
install(DIRECTORY qml
    DESTINATION share/ru.auroraos.aurora_test_app
)

foreach(_file IN LISTS QmFiles)
    get_filename_component(_filename "${_file}" NAME)
    string(REPLACE "${short_name}" "${PROJECT_NAME}" _full_filename "${_filename}")
    install(FILES ${_file} DESTINATION share/ru.auroraos.aurora_test_app/translations RENAME ${_full_filename})
endforeach()

set(IconSize 86x86 108x108 128x128 172x172)
foreach(_size IN LISTS IconSize)
    install(FILES icons/${_size}/ru.auroraos.aurora_test_app.png DESTINATION share/icons/hicolor/${_size}/apps)
endforeach()

install(FILES ru.auroraos.aurora_test_app.desktop
    DESTINATION share/applications
)

# Get the other files reachable from the project tree in Qt Creator
FILE(GLOB_RECURSE IconFiles "icons" "*.png")
set(RESOURCE_FILES ${IconFiles})

FILE(GLOB TsFiles "translations/*.ts")
add_custom_target(distfiles
    SOURCES
        AUTHORS.md
        CODE_OF_CONDUCT.md
        CONTRIBUTING.md
        LICENSE.BSD-3-CLAUSE.md
        README.md
        ru.auroraos.aurora_test_app.desktop
        qml/aurora_test_app.qml
        qml/cover/DefaultCoverPage.qml
        qml/pages/MainPage.qml
        qml/pages/AboutPage.qml
        rpm/ru.auroraos.aurora_test_app.spec
        rpm/ru.auroraos.aurora_test_app.changes.in
        rpm/ru.auroraos.aurora_test_app.changes.run.in
        ${TsFiles}
        ${IconFiles})

file(WRITE "${CMAKE_BINARY_DIR}/QtCreatorDeployment.txt"
    "${CMAKE_INSTALL_PREFIX}
${CMAKE_BINARY_DIR}/ru.auroraos.aurora_test_app:bin
")

