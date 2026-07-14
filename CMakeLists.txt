cmake_minimum_required(VERSION 3.17)
project(mtpfs)
set(MTPFS_NAME simple-mtpfs)

set(CMAKE_CXX_STANDARD 17)
set(FUSE_OPTIONS
  -D_FILE_OFFSET_BITS=64
  -DFUSE_USE_VERSION=28
  -DDEBUG_V8
)

find_library(LIBUSB
  NAMES usb usb-1.0
  PATH_SUFFIXES "lib" "lib32" "lib64"
  REQUIRED
)
find_path(LIBUSB_INCLUDE_DIRS
  NAMES libusb.h
  PATH_SUFFIXES "include" "libusb" "libusb-1.0"
  REQUIRED
)

find_library(LIBFUSE
  NAMES fuse
  REQUIRED
)
find_path(FUSE_INCLUDE_DIRS
  NAMES fuse.h
  PATH_SUFFIXES "include"
  REQUIRED
)

find_library(LIBMTP
  NAMES mtp
  REQUIRED
)
find_path(LIBMTP_INCLUDE_DIRS
  NAMES libmtp.h
  PATH_SUFFIXES "include" "libmtp"
  REQUIRED
)


add_compile_options(
  ${FUSE_OPTIONS}
  -framework IOKit
  -DHAVE_LIBMTP=1
  -DHAVE_LIBMTP_H=1
  -DHAVE_LIBMTP_CHECK_CAPABILITY=1
  -DMTPFS_NOAPPLEDOUBLE=1
)

file (GLOB MPTFS_SRC "./src/*.cpp")
add_executable(${MTPFS_NAME} ${MPTFS_SRC})
target_include_directories(${MTPFS_NAME}
  PUBLIC
  ./
  ${FUSE_INCLUDE_DIRS}
  ${LIBUSB_INCLUDE_DIRS}
  ${LIBMTP_INCLUDE_DIRS}
)
target_link_libraries(${MTPFS_NAME}
  ${LIBUSB}
  ${LIBMTP}
  ${LIBFUSE}
)
