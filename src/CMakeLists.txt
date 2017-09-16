include_directories(${CMAKE_CURRENT_SOURCE_DIR})
include_directories(${CMAKE_CURRENT_SOURCE_DIR}/src/)
message(${CMAKE_CURRENT_SOURCE_DIR})

add_subdirectory(languages/csharp)
add_subdirectory(languages/java)
