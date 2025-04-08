cmake_minimum_required(VERSION 3.8)

PROJECT(sqlite3 LANGUAGES C)

add_library(sqlite3
  src/sqlite3.c
  src/sqlite3.h
  src/sqlite3ext.h
)

target_include_directories(sqlite3 PUBLIC src/)

target_compile_definitions(sqlite3
  PRIVATE -DSQLITE_ENABLE_RTREE
  PRIVATE -DSQLITE_ENABLE_FTS4
  PRIVATE -DSQLITE_ENABLE_FTS5
  PRIVATE -DSQLITE_ENABLE_JSON1
  PRIVATE -DSQLITE_ENABLE_RBU
  PRIVATE -DSQLITE_ENABLE_STAT4
)

# Uncomment this for single-threaded variant (faster)
#target_compile_definitions(sqlite3 PRIVATE -DSQLITE_THREADSAFE=0) #=1 ????

add_executable(sqlite src/shell.c)

target_link_libraries(sqlite
  PRIVATE sqlite3
)

if(WIN32)
  add_custom_command(TARGET sqlite POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy $<TARGET_FILE:sqlite> ${CMAKE_BINARY_DIR}/sqlite3.exe
    DEPENDS sqlite
  )
  install(FILES sqlite3.exe DESTINATION bin)
else()
  include(FindThreads)
  target_link_libraries(sqlite PRIVATE m ${CMAKE_THREAD_LIBS_INIT} ${CMAKE_DL_LIBS})
  install(TARGETS sqlite RUNTIME DESTINATION bin)
endif()

install(FILES src/sqlite3.h src/sqlite3ext.h DESTINATION include)
install(TARGETS sqlite3 LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)
