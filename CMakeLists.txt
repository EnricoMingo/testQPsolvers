#
# Copyright 2014 CNRS
# Author: Andrea Del Prete
#

cmake_minimum_required(VERSION 2.8.12)
if(COMMAND cmake_policy)
    cmake_policy(SET CMP0003 OLD)
    cmake_policy(SET CMP0005 NEW)
    cmake_policy(SET CMP0017 NEW)
endif(COMMAND cmake_policy)
include(ExternalProject)

if (CMAKE_BUILD_TYPE STREQUAL "Debug")
    set(CMAKE_CXX_FLAGS "${CMAKE_CXX_FLAGS} -Werror=return-type -Werror=address -Werror=parentheses " )
endif(CMAKE_BUILD_TYPE STREQUAL "Debug")

list(APPEND CMAKE_MODULE_PATH "${CMAKE_CURRENT_SOURCE_DIR}/cmake/Modules/")
SET(PROJECT_NAME testQPsolvers)
SET(PROJECT_DESCRIPTION "Tests of different QP solvers")
SET(PROJECT_URL "http://github.com/andreadelprete/testQPsolvers")


SET(CXX_DISABLE_WERROR True)
SET(CMAKE_VERBOSE_MAKEFILE true)

project(${PROJECT_NAME})

#SETUP_PROJECT()
FIND_PACKAGE(Eigen3 REQUIRED)
FIND_PACKAGE(OpenSoT REQUIRED)

file(GLOB_RECURSE testQPsolvers_INCLUDES "${CMAKE_CURRENT_SOURCE_DIR}/include" *.h)

include_directories(include ${EIGEN3_INCLUDE_DIR})

ADD_LIBRARY(testQPsolvers_libs SHARED
			       src/testEigenEq.cpp
			       src/testQpOases.cpp
                               src/testQuadProgPP.cpp
                               ${testQPsolvers_INCLUDES})
ADD_EXECUTABLE(testQPsolvers src/main.cpp)

#file(GLOB ${PROJECT_NAME}_H    "include/testQPsolvers/*.hpp")
#file(GLOB ${PROJECT_NAME}_SRC  "main.cpp"  
#			       "testEigenEq.cpp"
#                               #"testMosek.cpp"
#                               #"testOOQP.cpp"
#                               "testQpOases.cpp"
#                               "testQuadProgPP.cpp")

# Find package qpOASES or build it using ExternalProject

set(qpOASES_SOURCE_DIR "$ENV{ROBOTOLOGY_ROOT}/external/OpenSoT/external/qpOASES-ext/")
set(qpOASES_INCLUDE_DIRS "${qpOASES_SOURCE_DIR}/include")


include_directories("${qpOASES_INCLUDE_DIRS}")

#INCLUDE_DIRECTORIES(${CMAKE_SOURCE_DIR}/include
                    #/home/adelpret/devel/yarp_gazebo/src/OOQP-0.99.24/include 
                    #/home/adelpret/devel/yarp_gazebo/src/mosek/7/tools/platform/linux64x86/h
#)

#link_directories(#/home/adelpret/devel/yarp_gazebo/src/OOQP-0.99.24/lib
                 #/home/adelpret/devel/yarp_gazebo/src/OOQP-0.99.24/extras/MA27
                 #/home/adelpret/devel/yarp_gazebo/src/mosek/7/tools/platform/linux64x86/bin)


target_link_libraries(testQPsolvers_libs $ENV{ROBOTOLOGY_ROOT}/build/external/OpenSoT/external/src/qpOASES-ext-build/libs/libqpOASES.a
				      blas gfortran
                                      #ooqpgendense ooqpdense 
				      #ooqpgensparse ooqpsparse 
                                      #ooqpgondzio ooqpbase   
                                      #libmosek64.so 
)

target_link_libraries(testQPsolvers   testQPsolvers_libs
				      $ENV{ROBOTOLOGY_ROOT}/build/external/OpenSoT/external/src/qpOASES-ext-build/libs/libqpOASES.a
				      blas gfortran
                                      #ooqpgendense ooqpdense 
				      #ooqpgensparse ooqpsparse 
                                      #ooqpgondzio ooqpbase   
                                      #libmosek64.so 
)

set_target_properties(${PROJECT_NAME} PROPERTIES LINKER_LANGUAGE CXX)

#PKG_CONFIG_APPEND_CFLAGS(${_Eigen_CFLAGS})
#SETUP_PROJECT_FINALIZE()
#SETUP_PROJECT_CPACK()

