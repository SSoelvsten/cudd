set(EPD_C_HEADERS
  epd.h
  epdInt.h
)

set(EPD_C_SOURCES
  epd.c
)

add_library(epd STATIC ${EPD_C_HEADERS} ${EPD_C_SOURCES})

target_link_libraries(epd PRIVATE util)

target_include_directories(epd PUBLIC ${CMAKE_CURRENT_BINARY_DIR}
                                      ${CMAKE_CURRENT_SOURCE_DIR})
