if (NOT WIN32)
    set(CMAKE_C_FLAGS "--std=gnu99 -fPIC ${CMAKE_C_FLAGS}")
else()
    set(CMAKE_C_FLAGS "--std=gnu99 ${CMAKE_C_FLAGS}")
endif()

find_library(zoflipng zopflipng /usr/local/lib)
find_library(jpeg jpeg /opt/mozjpeg/lib)
find_library(turbojpeg turbojpeg /opt/mozjpeg/lib)

add_library(caesium SHARED caesium.c error.c utils.c png.c lodepng.c jpeg.c)
add_library(caesium_static STATIC caesium.c error.c utils.c png.c lodepng.c jpeg.c)

set_target_properties(caesium_static PROPERTIES OUTPUT_NAME caesium)

target_link_libraries(caesium zopflipng jpeg turbojpeg)

# Make sure the compiler can find include files for our Caesium library
# when other libraries or executables link to Caesium
target_include_directories(caesium PUBLIC ${CMAKE_CURRENT_SOURCE_DIR})

# OSX installs in local
if (APPLE)
    install(FILES caesium.h DESTINATION /usr/local/include)
    install(TARGETS caesium caesium_static
            LIBRARY DESTINATION /usr/local/lib
            ARCHIVE DESTINATION /usr/local/lib)
elseif (UNIX OR LINUX)
    install(FILES caesium.h DESTINATION /usr/include)
    install(TARGETS caesium caesium_static
            LIBRARY DESTINATION /usr/lib
            ARCHIVE DESTINATION /usr/lib)
else ()
    install(FILES caesium.h DESTINATION include)
    install(TARGETS caesium caesium_static
            LIBRARY DESTINATION lib
            ARCHIVE DESTINATION lib)
endif ()