cmake_minimum_required(VERSION 2.8)

list(APPEND CMAKE_MODULE_PATH "${CMAKE_SOURCE_DIR}/cmake")

find_package(CSharp REQUIRED)
set(CMAKE_FIND_LIBRARY_PREFIXES "lib" "")
set(CMAKE_FIND_LIBRARY_SUFFIXES ".dll.a" ".a" ".lib")
include(UseCSharp)
include_directories(${CSHARP_INCLUDE_DIRS})

SET_SOURCE_FILES_PROPERTIES(src/languages/example.i PROPERTIES CPLUSPLUS ON) 

set( CMAKE_SWIG_OUTDIR ${CMAKE_CURRENT_BINARY_DIR} )

swig_add_module(
    example_cs
    csharp 
    ${CMAKE_SOURCE_DIR}/src/languages/example.i 
    ${CMAKE_SOURCE_DIR}/src/example.c
)

# For convenience we copy the dynamic library to the current build folder
add_custom_command(
    TARGET example_cs
    POST_BUILD
    COMMAND ${CMAKE_COMMAND} -E copy_if_different $<TARGET_FILE:example_cs> ${CMAKE_CURRENT_BINARY_DIR}
)


add_custom_command(
    TARGET example_cs
    POST_BUILD
    COMMAND ${CSHARP_COMPILER} /t:exe /out:main.exe example.cs examplePINVOKE.cs ${CMAKE_SOURCE_DIR}\\src\\languages\\csharp\\main.cs
)
