cmake_minimum_required(VERSION 3.9)

set(CMAKE_FIND_LIBRARY_PREFIXES "" "")
set(CMAKE_FIND_LIBRARY_SUFFIXES ".dll.a" ".a" ".lib")



find_package(Java REQUIRED)
find_package(JNI REQUIRED)
include(UseJava)
include_directories(${JNI_INCLUDE_DIRS})

swig_add_module(
    example
    java
    ${CMAKE_SOURCE_DIR}/src/languages/example.i
    ${CMAKE_SOURCE_DIR}/src/example.c
)

SET_TARGET_PROPERTIES(example PROPERTIES PREFIX "")

# add_library(example_java ${CMAKE_SOURCE_DIR}/src/example.c)

# Build the C++ code into a dynamic library: example.dll (on Windows) or libexample.so (on Linux)

# For convenience we copy the dynamic library to the current build folder
add_custom_command(
    TARGET example
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different $<TARGET_FILE:example> ${CMAKE_CURRENT_BINARY_DIR}
)

set(CMAKE_FIND_LIBRARY_PREFIXES "" "")
set(CMAKE_FIND_LIBRARY_SUFFIXES ".dll.a" ".a" ".lib")

set( CMAKE_SWIG_OUTDIR ${CMAKE_CURRENT_BINARY_DIR} )


# Build the Java code into ExampleJNI.jar
add_jar(
    ExampleJNI
    SOURCES
    ${PROJECT_BINARY_DIR}/example.java
    ${PROJECT_BINARY_DIR}/exampleJNI.java
)
add_dependencies( ExampleJNI example )

# Finally build the demonstration code into Main.jar
list(APPEND CMAKE_JAVA_INCLUDE_PATH ${PROJECT_BINARY_DIR})


set(CMAKE_JAVA_JAR_ENTRY_POINT main) 
add_jar(
    Main
    SOURCES main.java
    ENTRY_POINT main
)
add_dependencies( Main ExampleJNI )

