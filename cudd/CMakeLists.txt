set_property(GLOBAL PROPERTY USE_FOLDERS ON)

# ============================================================================ #
# List of all files

set(CUDD_C_HEADERS
  cudd.h
  cuddInt.h
)

set(CUDD_C_SOURCES
  cuddAddAbs.c
  cuddBddCorr.c
  cuddGenetic.c
  cuddReorder.c
  cuddZddGroup.c
  cuddAddApply.c
  cuddBddIte.c
  cuddGroup.c
  cuddSat.c
  cuddZddIsop.c
  cuddAddFind.c
  cuddBridge.c
  cuddHarwell.c
  cuddSign.c
  cuddZddLin.c
  cuddAddInv.c
  cuddCache.c
  cuddInit.c
  cuddSolve.c
  cuddZddMisc.c
  cuddAddIte.c
  cuddCheck.c
  cuddInteract.c
  cuddSplit.c
  cuddZddPort.c
  cuddAddNeg.c
  cuddClip.c
  cuddLCache.c
  cuddSubsetHB.c
  cuddZddReord.c
  cuddAddWalsh.c
  cuddCof.c
  cuddLevelQ.c
  cuddSubsetSP.c
  cuddZddSetop.c
  cuddAndAbs.c
  cuddCompose.c
  cuddLinear.c
  cuddSymmetry.c
  cuddZddSymm.c
  cuddAnneal.c
  cuddDecomp.c
  cuddLiteral.c
  cuddTable.c
  cuddZddUtil.c
  cuddApa.c
  cuddEssent.c
  cuddMatMult.c
  cuddUtil.c
  testcudd.c
  cuddAPI.c
  cuddExact.c
  cuddPriority.c
  cuddWindow.c
  testextra.c
  cuddApprox.c
  cuddExport.c
  cuddRead.c
  cuddZddCount.c
  cuddBddAbs.c
  cuddGenCof.c
  cuddRef.c
  cuddZddFuncs.c
)

if (CUDD_CPP)
  set(CUDD_CPP_HEADERS
    ../cplusplus/cuddObj.hh
  )

  set(CUDD_CPP_SOURCES
    ../cplusplus/cuddObj.cc
  )
else (CUDD_CPP)
  set(CUDD_CPP_HEADERS
  )

  set(CUDD_CPP_SOURCES
  )
endif (CUDD_CPP)

# ============================================================================ #
# Set up as library

if (CUDD_SHARED)
  add_library(cudd SHARED ${CUDD_C_HEADERS} ${CUDD_CPP_HEADERS} ${CUDD_C_SOURCES} ${CUDD_CPP_SOURCES})
else()
  add_library(cudd STATIC ${CUDD_C_HEADERS} ${CUDD_CPP_HEADERS} ${CUDD_C_SOURCES} ${CUDD_CPP_SOURCES})
endif()

target_link_libraries(cudd PRIVATE epd mtr st util)

if (CUDD_STATS)
  target_compile_definitions(cudd PUBLIC DD_STATS)
endif()

target_include_directories(cudd PUBLIC ${CMAKE_CURRENT_BINARY_DIR}
                                       ${CMAKE_CURRENT_SOURCE_DIR})
if (CUDD_CPP)
  target_include_directories(cudd PUBLIC ${CMAKE_CURRENT_BINARY_DIR}/../cplusplus
                                         ${CMAKE_CURRENT_SOURCE_DIR}/../cplusplus)
endif (CUDD_CPP)

set_target_properties(cudd PROPERTIES
  # Language settings
  LINKER_LANGUAGE CXX
  C_STANDARD 11
  C_STANDARD_REQUIRED ON
  CXX_STANDARD 11
  CXX_STANDARD_REQUIRED ON
  CXX_VISIBILITY_PRESET hidden

  # Linkage?
  FOLDER cudd

  # Other
  VERSION ${PROJECT_VERSION}
  POSITION_INDEPENDENT_CODE ON
)

include(GenerateExportHeader)
generate_export_header(cudd
  EXPORT_MACRO_NAME CUDD_API
)

install(TARGETS cudd
  ARCHIVE DESTINATION ${CMAKE_INSTALL_LIBDIR}
  LIBRARY DESTINATION ${CMAKE_INSTALL_LIBDIR}
  RUNTIME DESTINATION ${CMAKE_INSTALL_BINDIR}
  INCLUDES DESTINATION ${LIBLEGACY_INCLUDE_DIRS}
)
