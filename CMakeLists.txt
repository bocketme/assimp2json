cmake_minimum_required (VERSION 2.6)
project (assimp2json)

# The version number.
set (ASSIMP2JSON_VERSION_MAJOR 2)
set (ASSIMP2JSON_VERSION_MINOR 0)

set(CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake-modules")
set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/bin)
set( CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/lib )


# configure a header file to pass some of the CMake settings
# to the source code
configure_file (
  "${PROJECT_SOURCE_DIR}/assimp2json/version.h.in"
  "${PROJECT_SOURCE_DIR}/assimp2json/version.h"
  )

set ( ASSIMP_BUILD_ASSIMP_TOOLS OFF CACHE BOOL
	"If the supplementary tools for Assimp are built in addition to the library."
)

add_subdirectory (assimp)
set (EXTRA_LIBS ${EXTRA_LIBS} assimp)

include_directories("assimp/include")

set(SRC assimp2json/cencode.c
                assimp2json/json_exporter.cpp
                assimp2json/mesh_splitter.cpp)

set(ATOJSON_ENTRY   assimp2json/main.cpp)

set(ATOJSON_SRC node-binding/AssimpToJson.cpp
                                 node-binding/AssimpToJson.hpp
                                 node-binding/library.cpp node-binding/AssimpExporter.cpp node-binding/AssimpExporter.hpp)

add_library(Bocketassimp2json SHARED ${SRC} ${ATOJSON_SRC})
target_link_libraries (Bocketassimp2json  ${EXTRA_LIBS})
set_target_properties(Bocketassimp2json PROPERTIES SUFFIX ".module")

add_executable(assimp2json	${SRC} assimp2json/main.cpp)
target_link_libraries (assimp2json  ${EXTRA_LIBS})

if( MSVC_IDE )
   add_custom_command(
      TARGET assimp2json
      POST_BUILD
      COMMAND ${CMAKE_COMMAND} -E copy ${CMAKE_BINARY_DIR}/assimp/code/$(ConfigurationName)/assimp.dll ${CMAKE_BINARY_DIR}/$(ConfigurationName)/assimp.dll
   )
endif( MSVC_IDE )

INSTALL( TARGETS assimp2json 
	 LIBRARY DESTINATION ${ASSIMP_LIB_INSTALL_DIR}
	 RUNTIME DESTINATION ${ASSIMP_BIN_INSTALL_DIR})
