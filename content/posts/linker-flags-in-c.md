---
title: "Setting linker flags in CMake"
date: 2019-01-08T10:00:00-04:00
draft: true
tags: ["CMake", "C++", "Linking libraries", "CMakeLists.txt", "LDFLAGS"]
---

When working in a C or C++ project using CMake as a build process manager,
it is almost a given that a set of external libraries will be used to aid
the development of complex systems reliant on variety of functions such as
low-level system access, database connectivity, etc.

To be able to use external libraries in a standard Makefile, it is usually
done by setting a set of flags, canonically known as `LDFLAGS`. Here is an
example of a Makefile that links PostgreSQL driver libraries to the project as
well as the C++ Standard Library (which does not come pre-packaged in the case
of gcc compiler).

**Example Makefile**

{{<highlight bash>}}
CC=gcc
LDFLAGS=-lstdc++ -lpqxx -lpq

all: main

main:
	$(CC) $(LDFLAGS) main.cpp -o main
{{</highlight>}}

In CMake, to make the same configuration viable, here is a way that `CMakeLists.txt`
can be configured to produce the same result:

**Example CMakeLists.txt**

{{<highlight bash>}}
cmake_minimum_required(VERSION 3.13)
project(cpp_example)

set(CMAKE_CXX_STANDARD 14)
set(CMAKE_C_COMPILER gcc)
set(CMAKE_CXX_COMPILER gcc)

# Link stdc++, libpqxx, and libpq
set(CMAKE_EXE_LINKER_FLAGS "-lstdc++ -lpqxx -lpq")

add_executable(cpp_example main.cpp)
{{</highlight>}}
