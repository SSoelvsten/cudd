set(ST_C_HEADERS
  st.h
)

set(ST_C_SOURCES
  st.c
  #testst.c
)

add_library(st STATIC ${ST_C_HEADERS} ${ST_C_SOURCES})

target_link_libraries(st PRIVATE util)

target_include_directories(st PUBLIC ${CMAKE_CURRENT_BINARY_DIR}
                                     ${CMAKE_CURRENT_SOURCE_DIR})
