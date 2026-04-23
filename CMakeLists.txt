cmake_minimum_required(VERSION 3.10)

project(vcpkg_test)

find_package(fmt CONFIG REQUIRED)
find_package(vcpkg_sample_lib CONFIG REQUIRED)  # Add this line

add_executable(vcpkg_test main.cpp)
target_link_libraries(vcpkg_test PRIVATE fmt::fmt)
target_link_libraries(vcpkg_test PRIVATE vcpkg_sample_lib::vcpkg_sample_lib)