# project info
PROJECT( sbml2matlab )
CMAKE_MINIMUM_REQUIRED( VERSION 2.8 )

# dependencies
# it will expect a CMakeLists.txt in each folder
ADD_SUBDIRECTORY(NOMLib)

# dependency headers
INCLUDE_DIRECTORIES(
    ${PROJECT_SOURCE_DIR}/NOMLib
    ${LIBSBML_INCLUDE}
)

link_directories(${LIBSBML_LIB})

SET(BUILD_SBML2MATLAB_AS "Executable" CACHE STRING "Build sbml2matlab as an executable or library")
SET_PROPERTY(CACHE BUILD_SBML2MATLAB_AS PROPERTY STRINGS Executable SharedLibrary StaticLibrary) 


if (BUILD_SBML2MATLAB_AS MATCHES "Executable")
# build executable
ADD_EXECUTABLE( sbml2matlab
    ${PROJECT_SOURCE_DIR}/*.h
    ${PROJECT_SOURCE_DIR}/*.cpp
)
install(TARGETS sbml2matlab RUNTIME DESTINATION .)
endif ()
if (BUILD_SBML2MATLAB_AS MATCHES "StaticLibrary")
# build static library
ADD_LIBRARY( sbml2matlab STATIC
    ${PROJECT_SOURCE_DIR}/*.h
    ${PROJECT_SOURCE_DIR}/*.cpp
)
install(TARGETS sbml2matlab ARCHIVE DESTINATION .)
endif ()
if(BUILD_SBML2MATLAB_AS MATCHES "SharedLibrary")
# build shared library
ADD_LIBRARY( sbml2matlab SHARED
    ${PROJECT_SOURCE_DIR}/*.h
    ${PROJECT_SOURCE_DIR}/*.cpp
)
install(TARGETS sbml2matlab RUNTIME DESTINATION .)
endif ()

install(FILES README.txt DESTINATION .)
install(FILES LICENSE.txt DESTINATION .)

# link to dependencies
TARGET_LINK_LIBRARIES(sbml2matlab NOM)

#---------------------CPACK---------------------
SET(CPACK_PACKAGE_DESCRIPTION_SUMMARY "Translate SBML files to MATLAB functions")
SET(CPACK_PACKAGE_VENDOR "Stanley Gu")
SET(CPACK_PACKAGE_DESCRIPTION_FILE "${CMAKE_CURRENT_SOURCE_DIR}/README.txt")
SET(CPACK_RESOURCE_FILE_LICENSE "${CMAKE_CURRENT_SOURCE_DIR}/LICENSE.txt")
SET(CPACK_PACKAGE_VERSION_MAJOR "1")
SET(CPACK_PACKAGE_VERSION_MINOR "0")
SET(CPACK_PACKAGE_VERSION_PATCH "0")
SET(CPACK_PACKAGE_INSTALL_DIRECTORY "sbml2matlab-${CPACK_PACKAGE_VERSION_MAJOR}.${CPACK_PACKAGE_VERSION_MINOR}.${CPACK_PACKAGE_VERSION_PATCH}")
IF(WIN32 AND NOT UNIX)
  # There is a bug in NSI that does not handle full unix paths properly. Make
  # sure there is at least one set of four (4) backlasshes.
  # SET(CPACK_PACKAGE_ICON "${CMake_SOURCE_DIR}/Utilities/Release\\\\InstallIcon.bmp")
  SET(CPACK_NSIS_INSTALLED_ICON_NAME "sbml2matlab.exe")
  SET(CPACK_NSIS_DISPLAY_NAME "${CPACK_PACKAGE_INSTALL_DIRECTORY}")
  SET(CPACK_NSIS_HELP_LINK "http:\\\\\\\\code.google.com/p/snowburst")
  SET(CPACK_NSIS_CONTACT "stanleygu@gmail.com")
  SET(CPACK_NSIS_MODIFY_PATH ON)
ELSE(WIN32 AND NOT UNIX)
  SET(CPACK_STRIP_FILES "bin/sbml2matlab")
  SET(CPACK_SOURCE_STRIP_FILES "")
ENDIF(WIN32 AND NOT UNIX)
SET(CPACK_PACKAGE_EXECUTABLES "sbml2matlab" "sbml2matlab")
SET(CPACK_NSIS_CREATE_ICONS_EXTRA "CreateShortCut '\$SMPROGRAMS\\\\$STARTMENU_FOLDER\\\\README.lnk'  '\$INSTDIR\\\\README.txt'")
INCLUDE(CPack)