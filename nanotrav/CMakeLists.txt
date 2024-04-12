set(NANOTRAV_C_HEADERS
  bnet.h
  ntr.h
)

set(NANOTRAV_C_SOURCES
  bnet.c
  chkMterm.c
  ntr.c
  ntrBddTest.c
  ntrHeap.c
  ntrMflow.c
  ntrShort.c
  ntrZddTest.c
  main.c
)

add_executable(nanotrav ${NANOTRAV_C_HEADERS} ${NANOTRAV_C_SOURCES})
target_link_libraries(nanotrav PRIVATE epd mtr st cudd util dddmp)
