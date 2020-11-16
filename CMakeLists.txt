cmake_minimum_required (VERSION 3.2)
project (example-ios C CXX)
enable_testing()

MESSAGE( STATUS "CMAKE_CXX_FLAGS: " ${CMAKE_CXX_FLAGS} )

# Add some sanitary checks that the toolchain is actually working!
include(CheckCXXSymbolExists)
check_cxx_symbol_exists(kqueue sys/event.h HAVE_KQUEUE)
if(NOT HAVE_KQUEUE)
  message(FATAL_ERROR "kqueue NOT found!")
else()
  message(STATUS "kqueue found!")
endif()

find_library(HAVE_UIKIT UIKit)
if (NOT HAVE_UIKIT)
  message(FATAL_ERROR "UIKit.framework NOT found!")
else()
  message(STATUS "UIKit.framework found!")
endif()

# Hook up XCTest for the supported plaforms (all but WatchOS)
if(NOT PLATFORM MATCHES ".*WATCHOS.*")
  # Use the standard find_package, broken between 3.14.0 and 3.14.4 at least for XCtest...
  find_package(XCTest)
  # Fallback: Try to find XCtest as host package via toochain macro (should always work)
  find_host_package(XCTest REQUIRED)
endif()

# Includes
include_directories(${example-ios_SOURCE_DIR})

# Make sure try_compile() works
include(CheckTypeSize)
check_type_size(time_t SIZEOF_TIME_T)

# Source files
set(SOURCES
  HelloWorld.cpp
  HelloWorldIOS.mm
)

# Headers
set(HEADERS
  HelloWorld.hpp
  HelloWorldIOS.h
)

set(CMAKE_XCODE_ATTRIBUTE_DEVELOPMENT_TEAM "PSRT22QP6C" CACHE INTERNAL "")
#${HEADERS}必须加在工程里，否则framework里面无Headers
#${HEADERS}指定的文件必须在当前工程目录里，否则framework里面无Headers
#PUBLIC_HEADER 必须用文件名，用;分隔，不能使用${HEADERS}，全路径也不行
add_library (example STATIC ${SOURCES} ${HEADERS})
set_target_properties(example PROPERTIES
  FRAMEWORK TRUE
  FRAMEWORK_VERSION C
  MACOSX_FRAMEWORK_IDENTIFIER com.ftsafe.JubSDK
  #MACOSX_FRAMEWORK_INFO_PLIST Info.plist
  PUBLIC_HEADER "HelloWorld.hpp;HelloWorldIOS.h"
  XCODE_ATTRIBUTE_CODE_SIGN_IDENTITY "Apple Development: zxckiller@163.com (BV9H9Z227X)"
)

if(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)
  set(CMAKE_INSTALL_PREFIX ${example-ios_SOURCE_DIR}/../example-app/example-lib CACHE PATH "Install path" FORCE)
endif(CMAKE_INSTALL_PREFIX_INITIALIZED_TO_DEFAULT)


