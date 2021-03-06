## Find source and headers
FILE(GLOB HEADERS
  *.h
  "${MTCL_INCLUDE_DIR}/*.h"
  "${STB_INCLUDE_DIR}/*.h"
)
FILE(GLOB_RECURSE SOURCES
  ctoy_native.c
  ../3rdparty/glad/src/glad.c
  ../../../src/*.cpp  
)

IF(CMAKE_HAS_SORT)
  LIST(SORT HEADERS)
  LIST(SORT SOURCES)
ENDIF(CMAKE_HAS_SORT)

## Build
INCLUDE_DIRECTORIES(
  ## Maratis
  ${MTCL_INCLUDE_DIR}

  ## 3rdparty
  ${GLFW_INCLUDE_DIR}
  ${TINYCTHREAD_INCLUDE_DIR}
  ${GLAD_INCLUDE_DIR}
  ../3rdparty/libtcc/
  ../3rdparty/openal-soft/include/
)

IF(WIN32)
INCLUDE_DIRECTORIES(../3rdparty/dirent/include)
ENDIF(WIN32)

IF(WIN32)
  LINK_DIRECTORIES(../3rdparty/libtcc/win)
	LINK_DIRECTORIES(../3rdparty/openal-soft/lib/)
	SET(OPENAL_LIBRARY OpenAL32)
ENDIF(WIN32)
IF(APPLE)
  LINK_DIRECTORIES(../3rdparty/libtcc/osx)
	FIND_LIBRARY(OPENAL_LIBRARY OpenAL)
ENDIF(APPLE)

ADD_EXECUTABLE(CToy ${SOURCES} ${HEADERS})

TARGET_LINK_LIBRARIES(CToy
  ## Maratis
  ${MTCL_LIBRARIES}

  ## 3rdparty
  ${GLFW_LIBRARIES}
  ${TINYCTHREAD_LIBRARIES}
  tcc

  ## System libraries
  ${OPENGL_LIBRARIES}
  ${OPENAL_LIBRARY}
  ${PLATFORM_LIBRARIES}
  ${OS_SPECIFIC_LIBRARIES}
)

## Install
INSTALL(TARGETS CToy DESTINATION ${MTCL_INSTALL_DIR})

IF(WIN32)
  INSTALL(FILES ../3rdparty/libtcc/win/libtcc.dll DESTINATION ${MTCL_INSTALL_DIR})
  INSTALL(FILES ../3rdparty/libtcc/win/libtcc1.a DESTINATION ${MTCL_INSTALL_DIR})
  INSTALL(FILES ../3rdparty/openal-soft/lib/OpenAL32.dll DESTINATION ${MTCL_INSTALL_DIR})
ENDIF(WIN32)
IF(APPLE)
  INSTALL(FILES ../3rdparty/libtcc/osx/libtcc.dylib DESTINATION ${MTCL_INSTALL_DIR})
  INSTALL(FILES ../3rdparty/libtcc/osx/libtcc1.a DESTINATION ${MTCL_INSTALL_DIR})
ENDIF(APPLE)
IF(UNIX AND NOT APPLE)
  INSTALL(FILES ../3rdparty/libtcc/linux/libtcc.so DESTINATION ${MTCL_INSTALL_DIR})
  INSTALL(FILES ../3rdparty/libtcc/linux/libtcc1.a DESTINATION ${MTCL_INSTALL_DIR})
ENDIF(UNIX AND NOT APPLE)

INSTALL(DIRECTORY ../../../data DESTINATION ${MTCL_INSTALL_DIR})

IF(APPLE)
  SET_TARGET_PROPERTIES(
    CToy
    PROPERTIES
    BUILD_WITH_INSTALL_RPATH 1
    INSTALL_RPATH "@loader_path/"
  )
ENDIF(APPLE)

IF(UNIX AND NOT APPLE)
  SET_TARGET_PROPERTIES(
    CToy
    PROPERTIES
    BUILD_WITH_INSTALL_RPATH 1
    INSTALL_RPATH "$ORIGIN/"
  )
ENDIF(UNIX AND NOT APPLE)
