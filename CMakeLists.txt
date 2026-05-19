cmake_minimum_required(VERSION 3.10)
project(print VERSION 0.1.0)

set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

set(SOURCES print.cpp)
set(HEADERS print.h)
set(DEMO_SOURCES demo.cpp)

add_library(print STATIC ${SOURCES} ${HEADERS})

add_executable(demo ${DEMO_SOURCES})
target_link_libraries(demo print)

install(TARGETS print DESTINATION lib)
install(FILES ${HEADERS} DESTINATION include)

set(CPACK_GENERATOR "TGZ")
set(CPACK_PACKAGE_NAME "print")
set(CPACK_PACKAGE_VERSION "0.1.0")
set(CPACK_PACKAGE_FILE_NAME "print-${CPACK_PACKAGE_VERSION}-${CMAKE_SYSTEM_NAME}-${CMAKE_SYSTEM_PROCESSOR}")
include(CPack)
