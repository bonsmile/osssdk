#
# Copyright 2009-2017 Alibaba Cloud All rights reserved.
# 
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
# 
#      http://www.apache.org/licenses/LICENSE-2.0
# 
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#
cmake_minimum_required(VERSION 3.25.0)

project(osssdk CXX)

find_package(OpenSSL REQUIRED)
find_package(CURL CONFIG REQUIRED)

configure_file(src/Config.h.in ${CMAKE_CURRENT_SOURCE_DIR}/include/alibabacloud/oss/Config.h @ONLY)

#common header
file(GLOB sdk_auth_header "include/alibabacloud/oss/auth/*.h")
file(GLOB sdk_client_header "include/alibabacloud/oss/client/*.h")	
file(GLOB sdk_http_header "include/alibabacloud/oss/http/*.h")
file(GLOB sdk_utils_header "include/alibabacloud/oss/utils/*.h")
file(GLOB sdk_model_header "include/alibabacloud/oss/model/*.h")
file(GLOB sdk_encryption_header "include/alibabacloud/oss/encryption/*.h")	
file(GLOB sdk_public_header "include/alibabacloud/oss/*.h")	

#all header
file(GLOB sdk_header
	${sdk_auth_header}
	${sdk_client_header}
	${sdk_http_header}
	${sdk_utils_header}
	${sdk_model_header}
	${sdk_encryption_header}
    ${sdk_public_header}
)

#common source
file(GLOB sdk_auth_src "src/auth/*.cc")
file(GLOB sdk_client_src "src/client/*.cc")
file(GLOB sdk_http_src "src/http/*.cc")
file(GLOB sdk_utils_src "src/utils/*.cc")
file(GLOB sdk_model_src "src/model/*.cc")
file(GLOB sdk_encryption_src "src/encryption/*.cc")
file(GLOB sdk_public_src "src/*.cc")

#external source
file(GLOB sdk_external_json_src "src/external/json/*.cpp")
file(GLOB sdk_external_tinnyxml2_src "src/external/tinyxml2/*.cpp")

#all source
file(GLOB sdk_src
	${sdk_auth_src}
	${sdk_client_src}
	${sdk_http_src}
	${sdk_utils_src}
	${sdk_model_src}
	${sdk_encryption_src}
    ${sdk_public_src}
    ${sdk_external_json_src}
    ${sdk_external_tinnyxml2_src}
)

#extra define pass to source code
if (BUILD_TESTS)
	set(EXTRA_DEFINE "-DENABLE_OSS_TEST")
else()
	set(EXTRA_DEFINE "")
endif()

add_library(${PROJECT_NAME} STATIC
	${sdk_header}
	${sdk_src})

target_include_directories(${PROJECT_NAME}
	PRIVATE include
	PRIVATE include/alibabacloud/oss	
	PRIVATE src/external/)

target_include_directories(${PROJECT_NAME}
	PRIVATE ${CRYPTO_INCLUDE_DIRS}
	PRIVATE ${CLIENT_INCLUDE_DIRS})

target_compile_definitions(${PROJECT_NAME} PRIVATE -DALIBABACLOUD_SHARED -DALIBABACLOUD_OSS_LIBRARY)
target_compile_options(${PROJECT_NAME} PRIVATE "/D_SILENCE_ALL_CXX17_DEPRECATION_WARNINGS")

target_link_libraries(${PROJECT_NAME} OpenSSL::SSL OpenSSL::Crypto)
target_link_libraries(${PROJECT_NAME} CURL::libcurl)
