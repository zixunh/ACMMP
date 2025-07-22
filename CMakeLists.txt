cmake_minimum_required(VERSION 3.18)
project(ACMMP)

find_package(CUDA REQUIRED)
find_package(OpenCV REQUIRED)

include_directories(${OpenCV_INCLUDE_DIRS})
include_directories(.)

set(CUDA_NVCC_FLAGS ${CUDA_NVCC_FLAGS};
    -O3 --use_fast_math --maxrregcount=128 --ptxas-options=-v -std=c++11
    -gencode arch=compute_80,code=sm_80
    -gencode arch=compute_86,code=sm_86
    -gencode arch=compute_89,code=sm_89
)

if(CMAKE_COMPILER_IS_GNUCXX)
    add_definitions(-std=c++11)
    add_definitions(-pthread -Wall -Wextra -pedantic -Wno-unused-function -Wno-switch)
    set(CMAKE_CXX_FLAGS_RELEASE "${CMAKE_C_FLAGS_RELEASE} -O3 -ffast-math -march=native")
endif()

find_package(OpenMP)
if (OPENMP_FOUND)
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} ${OpenMP_CXX_FLAGS}")
endif()

cuda_add_executable(ACMMP
    main.h
    ACMMP.h
    ACMMP.cpp
    ACMMP.cu
    main.cpp
)

target_link_libraries(ACMMP
    ${OpenCV_LIBS}
)
