set(UTIL_C_HEADERS
  cstringstream.h
  util.h
)

set(UTIL_C_SOURCES
  cpu_stats.c
  cpu_time.c
  cstringstream.c
  datalimit.c
  pathsearch.c
  pipefork.c
  prtime.c
  safe_mem.c
  strsav.c
  texpand.c
  ucbqsort.c
)

# ============================================================================ #
# Set up as (static) library

add_library(util STATIC ${UTIL_C_HEADERS} ${UTIL_C_SOURCES})

target_include_directories(util PUBLIC ${CMAKE_CURRENT_BINARY_DIR}
                                       ${CMAKE_CURRENT_SOURCE_DIR})
