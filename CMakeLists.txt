cmake_minimum_required(VERSION 3.10)

project(vcpkg_sample_lib)

include(GNUInstallDirs)

find_package(fmt CONFIG REQUIRED)

add_library(vcpkg_sample_lib STATIC vcpkgsample.cpp)
if (BUILD_SHARED_LIBS AND MSVC)
    target_compile_definitions(vcpkg_sample_lib PRIVATE MYLIB_EXPORTS)
endif()
target_link_libraries(vcpkg_sample_lib PRIVATE fmt::fmt)
# Add include directories
target_include_directories(vcpkg_sample_lib PUBLIC 
    $<BUILD_INTERFACE:${CMAKE_CURRENT_SOURCE_DIR}>   # for headers when building
    $<INSTALL_INTERFACE:${CMAKE_INSTALL_INCLUDEDIR}>  # for client in install mode
)

# Install the library and its headers
install(TARGETS vcpkg_sample_lib
        EXPORT vcpkg_sample_lib_targets
        ARCHIVE DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        LIBRARY DESTINATION "${CMAKE_INSTALL_LIBDIR}"
        RUNTIME DESTINATION "${CMAKE_INSTALL_BINDIR}")

install(FILES vcpkgsample.h DESTINATION include)

# Generate and install *-targets.cmake 
install(EXPORT vcpkg_sample_lib_targets
        FILE vcpkg_sample_lib-targets.cmake
        NAMESPACE vcpkg_sample_lib::
        DESTINATION share/vcpkg_sample_lib)

# Generate the config file in the current binary dir (this ensures it's not placed directly in source)
file(WRITE "${CMAKE_CURRENT_BINARY_DIR}/vcpkg_sample_lib-config.cmake"
"include(CMakeFindDependencyMacro)\n"
"find_dependency(fmt CONFIG REQUIRED)\n"
"include(\"\${CMAKE_CURRENT_LIST_DIR}/vcpkg_sample_lib-targets.cmake\")\n"
)

# Install the generated config file
install(FILES "${CMAKE_CURRENT_BINARY_DIR}/vcpkg_sample_lib-config.cmake"
    DESTINATION share/vcpkg_sample_lib)
