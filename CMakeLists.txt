cmake_minimum_required(VERSION 3.10)
project(fuse0 C CXX)

set(CMAKE_CXX_STANDARD 20)

find_package(PkgConfig REQUIRED)
pkg_check_modules(fuse3 REQUIRED IMPORTED_TARGET fuse3)
set(CMAKE_THREAD_PREFER_PTHREAD TRUE)
set(THREADS_PREFER_PTHREAD_FLAG TRUE)
find_package(Threads REQUIRED)

add_executable(fuse0 fuse_main.c)
target_link_libraries(fuse0 PkgConfig::fuse3 Threads::Threads)
