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

project(com.den3000.BasicApp CXX)
cmake_minimum_required(VERSION 3.5)

find_package (Qt5 COMPONENTS Core Network Qml Gui Quick LinguistTools REQUIRED)

include(FindPkgConfig)
pkg_search_module(AURORA auroraapp REQUIRED)
pkg_search_module(AURORA auroraapp_i18n REQUIRED)

set(CMAKE_AUTOMOC ON)
set(CMAKE_INCLUDE_CURRENT_DIR ON)
set(CMAKE_CXX_STANDARD 17)

set(SOURCES
    src/main.cpp)

set(HEADERS
    src/aboutvm.h
    src/dicontainer.h
    src/diprovider.h
    src/easy_import.h
    src/mainvm.h
    src/mathservice.h)

set(short_name BasicApp)

FILE(GLOB TsFiles "translations/*.ts")
qt5_add_translation(QmFiles ${TsFiles})

add_executable(com.den3000.BasicApp ${SOURCES} ${HEADERS} ${QmFiles})
target_compile_definitions(com.den3000.BasicApp PRIVATE
    $<$<OR:$<CONFIG:Debug>,$<CONFIG:RelWithDebInfo>>:QT_QML_DEBUG>
)
target_include_directories(com.den3000.BasicApp PRIVATE
    $<BUILD_INTERFACE:
    ${AURORA_INCLUDE_DIRS}>
    PUBLIC include
    )
target_link_libraries(com.den3000.BasicApp
    Qt5::Quick
    ${AURORA_LDFLAGS}
)

install(TARGETS com.den3000.BasicApp
    RUNTIME DESTINATION bin
)
install(DIRECTORY qml
    DESTINATION share/com.den3000.BasicApp
)

foreach(_file IN LISTS QmFiles)
    get_filename_component(_filename "${_file}" NAME)
    string(REPLACE "${short_name}" "${PROJECT_NAME}" _full_filename "${_filename}")
    install(FILES ${_file} DESTINATION share/com.den3000.BasicApp/translations RENAME ${_full_filename})
endforeach()

install(FILES com.den3000.BasicApp.desktop
    DESTINATION share/applications
)
install(FILES icons/86x86/com.den3000.BasicApp.png
    DESTINATION share/icons/hicolor/86x86/apps
)
install(FILES icons/108x108/com.den3000.BasicApp.png
    DESTINATION share/icons/hicolor/108x108/apps
)
install(FILES icons/128x128/com.den3000.BasicApp.png
    DESTINATION share/icons/hicolor/128x128/apps
)
install(FILES icons/172x172/com.den3000.BasicApp.png
    DESTINATION share/icons/hicolor/172x172/apps
)

# Get the other files reachable from the project tree in Qt Creator
FILE(GLOB TsFiles "translations/*.ts")
add_custom_target(distfiles
    SOURCES
        AUTHORS.md
        CODE_OF_CONDUCT.md
        CONTRIBUTING.md
        LICENSE.BSD-3-CLAUSE.md
        README.md
        com.den3000.BasicApp.desktop
        qml/BasicAppV1.qml
    	qml/BasicAppV2.qml
    	qml/Coordinator.qml
    	qml/CoordinatorV2.qml
        qml/cover/DefaultCoverPage.qml
        qml/pages/AboutPageV1.qml
    	qml/pages/AboutPageV2.qml
    	qml/pages/MainPageV1.qml
    	qml/pages/MainPageV2.qml
        rpm/com.den3000.BasicApp.spec
        rpm/com.den3000.BasicApp.changes.in
        rpm/com.den3000.BasicApp.changes.run.in
        ${TsFiles})

file(WRITE "${CMAKE_BINARY_DIR}/QtCreatorDeployment.txt"
    "${CMAKE_INSTALL_PREFIX}
${CMAKE_BINARY_DIR}/com.den3000.BasicApp:bin
")

