cmake_minimum_required(VERSION 3.16)
project(https C)
set(CMAKE_C_STANDARD 99)

set(CMAKE_MODULE_PATH ${CMAKE_MODULE_PATH} "${CMAKE_SOURCE_DIR}/")
find_package(Lit REQUIRED)
find_package(OpenSSL REQUIRED)

include_directories(include/ ${OPENSSL_INCLUDE_DIR} ${LIT_INCLUDE_DIRS})
add_subdirectory(util/embed-file/)

add_custom_command(
	OUTPUT src/lit_https.c
	COMMAND embedfile src/ lit_https include/https.lit
	WORKING_DIRECTORY ${CMAKE_CURRENT_SOURCE_DIR}
	DEPENDS include/https.lit)

add_library(https SHARED src/https.c include/https.h src/lit_https.c)
target_link_libraries(https ${LIT_LIBRARIES} ${OPENSSL_LIBRARIES} m)

set_target_properties(https PROPERTIES LIBRARY_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}/dist")
install(TARGETS https DESTINATION lib/lit)

add_executable(test src/test.c)
set_target_properties(test PROPERTIES RUNTIME_OUTPUT_DIRECTORY "${CMAKE_CURRENT_SOURCE_DIR}")

target_link_libraries(test LINK_PUBLIC https)