# ------------------------------------------------------------------------------
#        A Modular Optimization framework for Localization and mApping
#                               (MOLA)
#
# Copyright (C) 2018-2019, Jose Luis Blanco-Claraco, contributors (AUTHORS.md)
# All rights reserved.
# Released under GNU GPL v3. See LICENSE file
# ------------------------------------------------------------------------------

# Minimum CMake vesion: limited by CMAKE_WINDOWS_EXPORT_ALL_SYMBOLS
cmake_minimum_required(VERSION 3.4)

# Tell CMake we'll use C++ for use in its tests/flags
project(mola-fe-lidar LANGUAGES CXX)

# MOLA CMake scripts: "mola_xxx()"
find_package(mola-common REQUIRED)

# find dependencies:
find_package(MRPT 1.9.9 REQUIRED COMPONENTS slam graphs gui tclap)

find_mola_package(mola-kernel REQUIRED)
find_mola_package(mola-lidar-segmentation REQUIRED)
find_mola_package(mp2p_icp REQUIRED)

find_package(yaml_cpp_catkin QUIET)
if(${yaml_cpp_catkin_FOUND})
  message(STATUS "Found yaml_cpp_catkin, using instead of system library.")
  set(YamlCpp_LIBRARIES ${yaml_cpp_catkin_LIBRARIES})
  set(YamlCpp_INCLUDE_DIRS ${yaml_cpp_catkin_INCLUDE_DIRS})
else()
  message(STATUS "No yaml_cpp_catkin, using yaml-cpp system library instead.")
  pkg_check_modules(YamlCpp REQUIRED yaml-cpp>=0.5)
endif()

# -----------------------
# define lib:
file(GLOB_RECURSE LIB_SRCS src/*.cpp src/*.h)
file(GLOB_RECURSE LIB_PUBLIC_HDRS include/*.h)

mola_add_library(
	TARGET ${PROJECT_NAME}
	SOURCES ${LIB_SRCS} ${LIB_PUBLIC_HDRS}
	PUBLIC_LINK_LIBRARIES
	mola-lidar-segmentation
	mp2p_icp
		mola-kernel
		mrpt::graphs
		mrpt::slam
)

# ----------------------
# define app targets:
mola_add_executable(
	TARGET  mola-app-fe-lidar-align
	SOURCES apps/mola-app-fe-lidar-align.cpp
	LINK_LIBRARIES
	mola-lidar-segmentation
	mp2p_icp
		mrpt::tclap
		mrpt::gui
		${YamlCpp_LIBRARIES}
		${PROJECT_NAME}
)
