# The MIT License (MIT)
#
# Copyright (c) 2020 Scott Aron Bloom
#
# Permission is hereby granted, free of charge, to any person obtaining a copy
# of this software and associated documentation files (the "Software"), to deal
# in the Software without restriction, including without limitation the rights
# to use, copy, modify, merge, publish, distribute, sub-license, and/or sell
# copies of the Software, and to permit persons to whom the Software is
# furnished to do so, subject to the following conditions:
#
# The above copyright notice and this permission notice shall be included in
# all copies or substantial portions of the Software.
#
# THE SOFTWARE IS PROVIDED "AS IS", WITHOUT WARRANTY OF ANY KIND, EXPRESS OR
# IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY,
# FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE
# AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER
# LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM,
# OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE
# SOFTWARE.

cmake_minimum_required(VERSION 3.1)
if(CMAKE_VERSION VERSION_LESS "3.7.0")
    set(CMAKE_INCLUDE_CURRENT_DIR ON)
endif()

project(BSACalc) 

SET(CMAKE_MODULE_PATH ${CMAKE_SOURCE_DIR}/Modules ${CMAKE_MODULE_PATH})

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED true)
find_package(Threads)
find_package(Qt5 COMPONENTS Core Widgets REQUIRED)

find_package(Deploy REQUIRED)

set_property(GLOBAL PROPERTY USE_FOLDERS ON)

SET(CMAKE_INSTALL_PREFIX ${CMAKE_BINARY_DIR}/Install)
IF(${CMAKE_INSTALL_CONFIG_NAME} MATCHES "^([Dd][Ee][Bb][Uu][Gg])$")
    SET(CMAKE_INSTALL_PREFIX ${CMAKE_INSTALL_PREFIX}.debug)
ENDIF()

INSTALL( CODE "
        MESSAGE( STATUS \"Making a \${CMAKE_INSTALL_CONFIG_NAME} install area\" )
        IF(\${CMAKE_INSTALL_CONFIG_NAME} MATCHES \"^([Dd][Ee][Bb][Uu][Gg])$\")
            SET(CMAKE_INSTALL_PREFIX \${CMAKE_INSTALL_PREFIX}.debug)
        ENDIF()
        MESSAGE( STATUS \"Installing to \${CMAKE_INSTALL_PREFIX}\" )
    ")

add_subdirectory(SABUtils)


include( include.cmake )
include(SABUtils/Project.cmake)

add_executable(BSACalc WIN32
    ${project_SRCS} 
    ${project_H}  
    ${qtproject_SRCS} 
    ${qtproject_QRC} 
    ${qtproject_QRC_SRCS} 
    ${qtproject_UIS_H} 
    ${qtproject_MOC_SRCS} 
    ${qtproject_H} 
    ${qtproject_UIS}
    ${qtproject_QRC_SOURCES}
    ${_CMAKE_FILES}
    ${_CMAKE_MODULE_FILES}
)
target_link_libraries( BSACalc 
    Qt5::Widgets
    Qt5::Core
    SABUtils
)
SET(CMAKE_INSTALL_SYSTEM_RUNTIME_DESTINATION .)

DeployQt( BSACalc . )
DeploySystem( BSACalc )

INSTALL(TARGETS ${PROJECT_NAME} RUNTIME DESTINATION . )
INSTALL(FILES ${CMAKE_CURRENT_BINARY_DIR}/Debug/${PROJECT_NAME}.pdb DESTINATION . CONFIGURATIONS Debug )


SET(CPACK_PACKAGE_VENDOR "BSA Troop 162")
SET(CPACK_PACKAGE_VERSION_MAJOR "1")
SET(CPACK_PACKAGE_VERSION_MINOR "0")
SET(CPACK_PACKAGE_VERSION_PATCH "0")
SET(CPACK_GENERATOR ZIP )
include(CPack)
STRING(APPEND _CPACK_OTHER_VARIABLES_ "\nset(CPACK_RUNNING 1)" )
#get_cmake_property(_variableNames VARIABLES)
#list (SORT _variableNames)
#foreach (_variableName ${_variableNames})
#    message(STATUS "${_variableName}=${${_variableName}}")
#endforeach()
