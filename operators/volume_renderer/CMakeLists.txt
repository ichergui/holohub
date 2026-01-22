# SPDX-FileCopyrightText: Copyright (c) 2023-2026, NVIDIA CORPORATION & AFFILIATES. All rights reserved.
# SPDX-License-Identifier: Apache-2.0
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
# http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
cmake_minimum_required(VERSION 3.20)
project(volume_renderer)

find_package(nvtx3 3.3.0 REQUIRED)
find_package(rapids_logger 0.2.0 REQUIRED)
find_package(rmm 25.10.0 REQUIRED)
find_package(Eigen3 REQUIRED)
find_package(holoscan 0.6 REQUIRED CONFIG
             PATHS "/opt/nvidia/holoscan" "/workspace/holoscan-sdk/install")

find_package(ZLIB REQUIRED)

find_package(claraviz REQUIRED)
find_package(nlohmann_json REQUIRED)
find_package(ZLIB REQUIRED)

add_library(volume_renderer SHARED
  dataset.cpp
  dataset.hpp
  video_buffer_blob.hpp
  volume_renderer.cpp
  volume_renderer.hpp
  )

add_library(holoscan::ops::volume_renderer ALIAS volume_renderer)

target_link_libraries(volume_renderer
  PRIVATE
    holoscan::core
    clara::viz::renderer
    clara::viz::core
    clara::viz::core::hw
    GXF::multimedia
    ZLIB::ZLIB
)

target_include_directories(volume_renderer INTERFACE ${CMAKE_CURRENT_SOURCE_DIR})
target_compile_definitions(volume_renderer PRIVATE HOLOSCAN_MAJOR_VERSION=${holoscan_VERSION_MAJOR})
target_compile_definitions(volume_renderer PRIVATE HOLOSCAN_MINOR_VERSION=${holoscan_VERSION_MINOR})

install(
  TARGETS volume_renderer
  EXPORT holoscan-ops
)

if(HOLOHUB_BUILD_PYTHON)
    add_subdirectory(python)
endif()
