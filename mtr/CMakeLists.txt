set(MTR_C_HEADERS
  mtr.h
  mtrInt.h
)

set(MTR_C_SOURCES
  mtrBasic.c
  mtrGroup.c
  #testmtr.c
)

add_library(mtr STATIC ${MTR_C_HEADERS} ${MTR_C_SOURCES})

target_link_libraries(mtr PRIVATE util)

target_include_directories(mtr PUBLIC ${CMAKE_CURRENT_BINARY_DIR}
                                      ${CMAKE_CURRENT_SOURCE_DIR})
