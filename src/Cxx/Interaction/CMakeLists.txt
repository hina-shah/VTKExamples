PROJECT (${WIKI}Interaction)

IF(NOT WikiExamples_BINARY_DIR)
FIND_PACKAGE(VTK REQUIRED)
IF(NOT VTK_USE_RENDERING)
  MESSAGE(FATAL_ERROR "Example ${PROJECT_NAME} requires VTK_USE_RENDERING.")
ENDIF(NOT VTK_USE_RENDERING)
INCLUDE(${VTK_USE_FILE})
ENDIF(NOT WikiExamples_BINARY_DIR)

if("${VTK_MAJOR_VERSION}.${VTK_MINOR_VERSION}" LESS 5.8)
  SET(KIT_LIBS vtkRendering vtkWidgets vtkHybrid)
else()
  SET(KIT_LIBS ${VTK_LIBRARIES})
endif()
#
# Build all .cxx files in the directory
FILE(GLOB ALL_FILES *.cxx)
foreach(SOURCE_FILE ${ALL_FILES})
  STRING(REPLACE ".cxx" "" TMP ${SOURCE_FILE})
  STRING(REPLACE ${CMAKE_CURRENT_SOURCE_DIR}/ "" EXAMPLE ${TMP})
  ADD_EXECUTABLE(${WIKI}${EXAMPLE} ${EXECUTABLE_FLAG} ${EXAMPLE}.cxx)
  TARGET_LINK_LIBRARIES(${WIKI}${EXAMPLE} ${KIT_LIBS})
endforeach(SOURCE_FILE)

if (BUILD_TESTING)
# Testing
SET(KIT Interaction)
SET(NEEDS_ARGS
  EllipticalButton
  ImageClip
  ImageRegion
)
SET(DATA ${WikiExamples_SOURCE_DIR}/src/Testing/Data)

ADD_TEST(${KIT}-EllipticalButton ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
  TestEllipticalButton ${DATA}/Yinyang.png)

ADD_TEST(${KIT}-ImageClip ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
  TestImageClip ${DATA}/Gourds2.jpg)

ADD_TEST(${KIT}-ImageRegion ${CMAKE_RUNTIME_OUTPUT_DIRECTORY}/${KIT}CxxTests
  TestImageRegion ${DATA}/Gourds2.jpg)

INCLUDE(${WikiExamples_SOURCE_DIR}/CMake/ExamplesTesting.cmake)

endif()
