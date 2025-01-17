cmake_minimum_required(VERSION 3.1.3)

set(CMAKE_CXX_FLAGS  "${Trilinos_CXX_COMPILER_FLAGS} ${CMAKE_CXX_FLAGS}")
set(CMAKE_C_FLAGS  "${Trilinos_C_COMPILER_FLAGS} ${CMAKE_C_FLAGS}")
set(CMAKE_Fortran_FLAGS  "${Trilinos_Fortran_COMPILER_FLAGS} ${CMAKE_Fortran_FLAGS}")

INCLUDE_DIRECTORIES (${Trilinos_INCLUDE_DIRS} ${Trilinos_TPL_INCLUDE_DIRS})
LINK_DIRECTORIES (${Trilinos_LIBRARY_DIRS} ${Trilinos_TPL_LIBRARY_DIRS})

include_directories(SGH_solver)
include_directories(${CMAKE_SOURCE_DIR}/Implicit-Lagrange)
add_executable(Fierro_Parallel_Explicit main.cpp Explicit_Solver.cpp)

if (CUDA)
  add_definitions(-DHAVE_CUDA=1)
elseif (HIP)
  add_definitions(-DHAVE_HIP=1)
elseif (OPENMP)
  add_definitions(-DHAVE_OPENMP=1)
elseif (THREADS)
  add_definitions(-DHAVE_THREADS=1)
endif()

add_subdirectory(SGH_solver)

if(BUILD_ELEMENTS)
target_link_libraries (Fierro_Parallel_Explicit swage)
target_link_libraries (Fierro_Parallel_Explicit elements)
target_link_libraries (Fierro_Parallel_Explicit matar)
else()
target_link_libraries (Fierro_Parallel_Explicit ${CMAKE_SOURCE_DIR}/Elements/build/swage/libswage.a)
target_link_libraries (Fierro_Parallel_Explicit ${CMAKE_SOURCE_DIR}/Elements/build/elements/libelements.a)
target_link_libraries (Fierro_Parallel_Explicit ${CMAKE_SOURCE_DIR}/Elements/build/matar/src/libmatar.a)
endif()

target_include_directories(Fierro_Parallel_Explicit PRIVATE ${MPI_CXX_INCLUDE_PATH} ${MPI_Fortran_INCLUDE_PATH} ${CMAKE_CURRENT_SOURCE_DIR})
target_compile_options (Fierro_Parallel_Explicit PRIVATE ${CMAKE_CXX_COMPILE_FLAGS})
target_link_libraries (Fierro_Parallel_Explicit ${MPI_CXX_LIBRARIES} ${MPI_CXX_LINK_FLAGS} ${MPI_Fortran_LIBRARIES} ${MPI_Fortran_LINK_FLAGS} ${Trilinos_LIBRARIES} ${Trilinos_TPL_LIBRARIES})
target_link_libraries (Fierro_Parallel_Explicit parallel_sgh_solver)