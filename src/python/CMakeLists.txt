set(quickfix_python_VERSION_MAJOR 16)
set(quickfix_python_VERSION_MINOR 0)
set(quickfix_python_VERSION_PATCH 1)
set(quickfix_python_VERSION
    ${quickfix_python_VERSION_MAJOR}.${quickfix_python_VERSION_MINOR}.${quickfix_python_VERSION_PATCH}
)

execute_process(COMMAND ${CMAKE_CURRENT_SOURCE_DIR}/swig.sh
                WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}/)

set(quickfix_python_SOURCES QuickfixPython.cpp)
set(quickfix_python_lib_name _${PROJECT_NAME})

set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS}")
message(STATUS "CXX Flags: ${CMAKE_CXX_FLAGS}")
add_library(${quickfix_python_lib_name} SHARED ${quickfix_python_SOURCES})

target_include_directories(
  ${quickfix_python_lib_name} PRIVATE ${PROJECT_SOURCE_DIR}
                                      ${PROJECT_SOURCE_DIR}/src/C++)

target_link_libraries(${quickfix_python_lib_name} ${PROJECT_NAME}
                      ${PYTHON_LIBRARY})

if(HAVE_SSL)
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DHAVE_SSL=1")
  target_link_libraries(${quickfix_python_lib_name} ${PROJECT_NAME}
                        ${OPENSSL_LIBRARIES})
endif()

if(HAVE_MYSQL)
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -DHAVE_MYSQL=1")
  target_link_libraries(${quickfix_python_lib_name} ${PROJECT_NAME}
                        ${MYSQL_CLIENT_LIBS})
endif()

if(HAVE_POSTGRESQL)
  # set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -HAVE_POSTGRESQL=1")
  target_link_libraries(${quickfix_python_lib_name} ${PROJECT_NAME}
                        ${PostgreSQL_LIBRARIES})
endif()

set_target_properties(
  ${quickfix_python_lib_name}
  PROPERTIES VERSION ${quickfix_python_VERSION}
             SOVERSION ${quickfix_python_VERSION_MAJOR}
             PREFIX "")

install(TARGETS ${quickfix_python_lib_name} DESTINATION lib/python)
install(
  DIRECTORY ${PROJECT_SOURCE_DIR}/src/python/
  DESTINATION lib/python
  FILES_MATCHING
  PATTERN "quickfix*.py"
  PATTERN test EXCLUDE)
