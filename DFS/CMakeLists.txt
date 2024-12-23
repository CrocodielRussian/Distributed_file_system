cmake_minimum_required(VERSION 3.5)

project(DFS VERSION 0.1 LANGUAGES CXX)

set(CMAKE_AUTOUIC ON)
set(CMAKE_AUTOMOC ON)
set(CMAKE_AUTORCC ON)

set(CMAKE_CXX_STANDARD 17)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(QT NAMES Qt6 Qt5 REQUIRED COMPONENTS Widgets Network Sql Concurrent)
find_package(Qt${QT_VERSION_MAJOR} REQUIRED COMPONENTS Widgets Network Sql Concurrent)

include_directories(
    include
    ../utils/include
)

set(RESOURCE_FILES
    ../assets/assets.qrc
)

set(PROJECT_SOURCES
        main.cpp
        include/adminwidget.hpp src/adminwidget.cpp
        include/connectionwidget.hpp src/connectionwidget.cpp
        include/loginwidget.hpp src/loginwidget.cpp
        include/mainwidget.hpp src/mainwidget.cpp
        include/networkmanager.hpp src/networkmanager.cpp
        include/registrationwidget.hpp src/registrationwidget.cpp
        include/window.hpp src/window.cpp

        ../utils/include/macros.hpp
        ../utils/include/logger.hpp ../utils/src/logger.cpp
)

if(WIN32)
    set(WINDOWS_RESOURCE_FILE "${CMAKE_CURRENT_SOURCE_DIR}/../assets/DFS.rc")
    list(APPEND PROJECT_SOURCES ${WINDOWS_RESOURCE_FILE})
endif()

if(${QT_VERSION_MAJOR} GREATER_EQUAL 6)
    qt_add_executable(DFS
        MANUAL_FINALIZATION
        ${PROJECT_SOURCES}
        ${RESOURCE_FILES}
    )
else()
    if(ANDROID)
        add_library(DFS SHARED
            ${PROJECT_SOURCES}
            ${RESOURCE_FILES}
        )
    else()
        add_executable(DFS
            ${PROJECT_SOURCES}
            ${RESOURCE_FILES}
        )
    endif()
endif()

target_link_libraries(
    DFS PRIVATE
        Qt${QT_VERSION_MAJOR}::Widgets
        Qt${QT_VERSION_MAJOR}::Network
        Qt${QT_VERSION_MAJOR}::Sql
        Qt${QT_VERSION_MAJOR}::Concurrent
)

if(${QT_VERSION} VERSION_LESS 6.1.0)
  set(BUNDLE_ID_OPTION MACOSX_BUNDLE_GUI_IDENTIFIER com.example.DFS)
endif()
set_target_properties(DFS PROPERTIES
    ${BUNDLE_ID_OPTION}
    MACOSX_BUNDLE_BUNDLE_VERSION ${PROJECT_VERSION}
    MACOSX_BUNDLE_SHORT_VERSION_STRING ${PROJECT_VERSION_MAJOR}.${PROJECT_VERSION_MINOR}
    MACOSX_BUNDLE TRUE
    WIN32_EXECUTABLE TRUE
)

include(GNUInstallDirs)
install(TARGETS DFS
    BUNDLE DESTINATION .
    LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
    RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
)

if(QT_VERSION_MAJOR EQUAL 6)
    qt_finalize_executable(DFS)
endif()
