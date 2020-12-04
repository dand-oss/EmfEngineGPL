cmake_minimum_required(VERSION 3.0.0)
project (emfengine CXX)

set(CMAKE_AUTOMOC ON)

option(BUILD_EXAMPLE "Build the example program" OFF)
option(BUILD_SHARED_LIBS "Build shared libraries" ON)

#find Qt
find_package(Qt5 
    COMPONENTS
     Core
     Gui
     Svg
     Widgets
     PrintSupport
     REQUIRED
)

set(SUBDIRS
    src
    example)
foreach(dir ${SUBDIRS})
    add_subdirectory(${dir})
endforeach()

#find libemf if this isn't a Windows system
if(NOT WIN32)
    find_port_pkg(emf "EMF")
    target_link_libraries(${libname} PUBLIC
        ports:emf
        )
else(NOT WIN32)
    target_link_libraries(${libname} PUBLIC
        gdi32
        )
endif(NOT WIN32)

if (WIN32)
  set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR})
else ()
  # If not on Windows, put them under more standard UNIX-like places.
  set(CMAKE_LIBRARY_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib/)
  set(CMAKE_RUNTIME_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/bin/)
  set(CMAKE_ARCHIVE_OUTPUT_DIRECTORY ${CMAKE_BINARY_DIR}/lib/)
endif ()
add_library(EmfEngine ${EMFENGINE_SRCS})
if(BUILD_EXAMPLE)
    add_executable(example ${EXAMPLE_SRCS})
    target_link_libraries(example
        ${EMFENGINE_LIBRARIES}
        emfengine)
endif()

target_link_libraries(${libname})

install(FILES src/EmfEngine.h DESTINATION include)
install(TARGETS EmfEngine LIBRARY DESTINATION lib ARCHIVE DESTINATION lib)

