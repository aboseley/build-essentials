cmake_minimum_required (VERSION 2.8)
include(ExternalProject)

set(INSTALL_DIR ${CMAKE_BINARY_DIR}/installed CACHE PATH "tools install location")

set(AUTO_ARGS
    CXXFLAGS=-I${INSTALL_DIR}/include
    LDFLAGS=-L${INSTALL_DIR}/lib
    LD_LIBRARY_PATH=${INSTALL_DIR}/lib
    PKG_CONFIG_PATH=${INSTALL_DIR}/lib/pkgconfig/  )

set(build_essential 
    flex bison make gcc cmake pkg-config automake autoconf m4 )

ExternalProject_Add(
   m4
   URL http://ftp.gnu.org/gnu/m4/m4-1.4.18.tar.xz
   URL_HASH MD5=57a7a224a822f94789a587ccbcedff69
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   make 
   DEPENDS m4
   URL http://ftp.gnu.org/gnu/make/make-4.1.tar.bz2
   URL_HASH MD5=57a7a224a822f94789a587ccbcedff69 
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   pkg-config 
   DEPENDS make 
   URL https://pkg-config.freedesktop.org/releases/pkg-config-0.29.2.tar.gz 
   URL_HASH MD5=f6e931e319531b736fadc017f470e68a
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure 
        --with-internal-glib
        --prefix=${INSTALL_DIR}
   BUILD_IN_SOURCE 1
)

ExternalProject_Add(
   autoconf
   DEPENDS pkg-config 
   URL http://ftp.gnu.org/gnu/autoconf/autoconf-2.69.tar.gz
   URL_HASH MD5=82d05e03b93e45f5a39b828dc9c6c29b
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   automake 
   DEPENDS autoconf 
   URL http://ftp.gnu.org/gnu/automake/automake-1.15.tar.gz
   URL_HASH MD5=716946a105ca228ab545fc37a70df3a3
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   bison 
   DEPENDS automake
   URL http://ftp.gnu.org/gnu/bison/bison-3.0.4.tar.gz
   URL_HASH MD5=a586e11cd4aff49c3ff6d3b6a4c9ccf8
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   flex 
   DEPENDS bison 
   URL https://github.com/westes/flex/files/981163/flex-2.6.4.tar.gz
   #URL_HASH MD5=5724bcffed4ebe39e9b55a9be80859ec
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   gcc
   DEPENDS autoconf 
   URL http://ftp.gnu.org/gnu/gcc/gcc-7.1.0/gcc-7.1.0.tar.bz2
   URL_MD5 6bf56a2bca9dac9dbbf8e8d1036964a8
   PATCH_COMMAND ./contrib/download_prerequisites
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure 
                     --prefix=${INSTALL_DIR}
                     --enable-languages=c,c++
                     --disable-multilib
)

ExternalProject_Add(
   cmake 
   DEPENDS gcc 
   URL https://cmake.org/files/v3.9/cmake-3.9.0.tar.gz
   #URL_HASH SHA256=b73f8c1029611df7ed81796bf5ca8ba0ef41c6761132340c73ffe42704f980fa 
   CONFIGURE_COMMAND  <SOURCE_DIR>/bootstrap --prefix=${INSTALL_DIR}
)
#### end-essentials

ExternalProject_Add(
   gawk 
   DEPENDS ${build_essential}
   URL http://gnu.uberglobalmirror.com/gawk/gawk-4.1.4.tar.xz 
   #URL_HASH MD5=55d37f4069502677f25d1340df8eec97
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   perl 
   DEPENDS ${build_essential}
   URL http://www.cpan.org/src/5.0/perl-5.26.0.tar.bz2
   #URL_HASH MD5=f67b152160431b3180fb766bdc2d02e2 
   BUILD_IN_SOURCE 1
   CONFIGURE_COMMAND ${AUTO_ARGS} sh Configure -des -Dprefix=${INSTALL_DIR} 
)

ExternalProject_Add(
   ncurses 
   DEPENDS ${build_essential} gawk
   URL http://ftp.gnu.org/gnu//ncurses/ncurses-6.0.tar.gz
   URL_HASH MD5=ee13d052e1ead260d7c28071f46eefb1
   PATCH_COMMAND patch -p1 < ${CMAKE_CURRENT_LIST_DIR}/ncurses.patch
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure 
      --prefix=${INSTALL_DIR}
      --with-shared
      --without-debug
      --without-normal
      --enable-pc-files
)

ExternalProject_Add(
   libtool
   DEPENDS ${build_essential}
   URL http://ftp.gnu.org/gnu/libtool/libtool-2.4.6.tar.gz
   URL_HASH MD5=addf44b646ddb4e3919805aa88fa7c5e
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   sed 
   DEPENDS ${build_essential}
   URL http://ftp.gnu.org/gnu/sed/sed-4.2.2.tar.bz2
   URL_HASH MD5=7ffe1c7cdc3233e1e0c4b502df253974
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
)

ExternalProject_Add(
   python
   DEPENDS ${build_essential} ncurses
   URL "http://paraview.org/files/dependencies/Python-2.7.11.tgz"
   URL_MD5 "6b6076ec9e93f05dd63e47eb9c15728b"
   CONFIGURE_COMMAND ${AUTO_ARGS} <SOURCE_DIR>/configure --prefix=${INSTALL_DIR}
   BUILD_IN_SOURCE 1
)
