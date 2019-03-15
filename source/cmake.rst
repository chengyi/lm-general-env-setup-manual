CMake使用简说
======================

CMake是一种优秀的跨平台的构建系统， 类似于Makefile, 是专为C/C++开发的一套构建系统。

它使用自己定义的一套脚本语言来描述构建关系，最终也是生成Makefile。所以当您拿到一份采用CMake构建的源码时，它的使用姿势通常是这样的：

.. code-block:: bash

   cmake .
   make

构建可执行文件
---------------------
一般来说，一个 ``CMakeLists.txt`` 文件长的是这样的：

.. code-block:: cmake

   cmake_minimum_required (VERSION 2.6)
   project (helloworld)

   set(HELLO_SRC hello.cpp)
   add_executable(hello ${HELLO_SRC})

* `cmake_minimum_required <https://cmake.org/cmake/help/v3.0/command/cmake_minimum_required.html?highlight=cmake_minimum_required>`_ 指定了CMake的最低版本， 使用低于这个版本的CMake将会报错。
* `project <https://cmake.org/cmake/help/v3.0/command/project.html>`_ 指定了项目名称。
* `set <https://cmake.org/cmake/help/v3.0/command/set.html>`_ 是CMake脚本语言中用来给变量赋值的函数。
* `add_executable <https://cmake.org/cmake/help/v3.0/command/add_executable.html>`_ 用来告诉CMake为我们用 ``HELLO_SRC`` 中的源文件生成一个可执行文件。

构建动态库和静态库
--------------------
在CMake中提供了 `add_library <https://cmake.org/cmake/help/v3.0/command/add_library.html>`_ 这个函数来创建库， 具体用法如下：

.. code-block:: cmake

   # build shared library
   add_library(LIB_NAME SHARED ${LIB_SRC})
   # build static library
   add_library(LIB_NAME STATIC ${LIB_SRC})

.. warning::

   在实际的环境中， CMake的目标名必须唯一，上面所示代码块中的 ``LIB_NAME`` 出现了两次，只是为了演示目的，在真实的环境中，这样做是会被报错的。

链接
--------------------
CMake中我们使用 `target_link_libraries <https://cmake.org/cmake/help/v3.0/command/target_link_libraries.html>`_ 来进行链接操作。

.. code-block:: cmake

   target_link_libraries(hello LIB_NAME)


交叉编译的注意事项
____________________
在使用Yocto工具链进行交叉编译时，应当 **避免** source其自带的 ``environment-setup-cortexa9hf-vfp-neon-poky-linux-gnueabi`` 脚本后，直接cmake。原因是该脚本只是设置了 ``CC`` 等相关的环境变量，而CMake并不认为自身处于交叉编译的状态之下。这将可能导致一系列错误。

正确的做法是使用 ``-DCMAKE_TOOLCHAIN_FILE=path/to/file`` 来指定编译时的交叉编译工具链:

.. code-block:: shell

   cmake -DCMAKE_TOOLCHAIN_FILE=path/to/file .

下面附上我自己写的 ``toolchain.cmake`` :

.. code-block:: cmake

   SET(CMAKE_SYSTEM_NAME Linux)

   set(CROSS_COMPILE_TOOLCHAIN_PATH $ENV{OECORE_NATIVE_SYSROOT}/usr/bin/aarch64-poky-linux)
   set(CROSS_COMPILE_TOOLCHAIN_PREFIX "aarch64-poky-linux")
   set(CROSS_COMPILE_SYSROOT $ENV{OECORE_TARGET_SYSROOT})

   set(CMAKE_SYSROOT $ENV{OECORE_TARGET_SYSROOT})
   set(CMAKE_PREFIX_PATH ${CMAKE_SYSROOT}/usr/lib/cmake)
   set(CMAKE_FIND_ROOT_PATH $ENV{OECORE_TARGET_SYSROOT})
   set(CMAKE_FIND_ROOT_PATH_MODE_PROGRAM NEVER)
   set(CMAKE_FIND_ROOT_PATH_MODE_LIBRARY ONLY)
   set(CMAKE_FIND_ROOT_PATH_MODE_INCLUDE ONLY)
   set(CMAKE_FIND_ROOT_PATH_MODE_PACKAGE ONLY)

   if(DEFINED POLLY_COMPILER_GCC_CROSS_COMPILE)
   return()
   else()
   set(POLLY_COMPILER_GCC_CROSS_COMPILE TRUE)
   endif()

   if( CMAKE_TOOLCHAIN_FILE )
   # touch toolchain variable to suppress "unused variable" warning
   endif()

   string(COMPARE EQUAL
       "${CROSS_COMPILE_TOOLCHAIN_PATH}"
       ""
       _is_empty
   )
   if(_is_empty)
   message(FATAL_ERROR "CROSS_COMPILE_TOOLCHAIN_PATH not set.")
   endif()

   string(COMPARE EQUAL
       "${CROSS_COMPILE_TOOLCHAIN_PREFIX}"
       ""
       _is_empty
   )
   if(_is_empty)
   message(FATAL_ERROR "CROSS_COMPILE_TOOLCHAIN_PREFIX not set.")
   endif()

   string(COMPARE EQUAL
       "${CROSS_COMPILE_SYSROOT}"
       ""
       _is_empty
   )
   if(_is_empty)
   message(FATAL_ERROR "CROSS_COMPILE_SYSROOT not set.")
   endif()

   if(POLLY_SKIP_SYSROOT)
   # Do not modify CMAKE_{C,CXX}_FLAGS
   # Workaround for x86_64-pc-linux-gcc error:
   #   "this linker was not configured to use sysroots"
   else()
   set(SYSROOT_COMPILE_FLAG "--sysroot=${CROSS_COMPILE_SYSROOT}")
   endif()

   # The (...)_PREFIX variable name refers to the Cross Compiler Triplet
   set(TOOLCHAIN_PATH_AND_PREFIX ${CROSS_COMPILE_TOOLCHAIN_PATH}/${CROSS_COMPILE_TOOLCHAIN_PREFIX})
   set(CMAKE_C_COMPILER     "${TOOLCHAIN_PATH_AND_PREFIX}-gcc"     CACHE PATH "C compiler" )
   set(CMAKE_CXX_COMPILER   "${TOOLCHAIN_PATH_AND_PREFIX}-g++"     CACHE PATH "C++ compiler" )
   set(CMAKE_ASM_COMPILER   "${TOOLCHAIN_PATH_AND_PREFIX}-as"      CACHE PATH "Assembler" )
   set(CMAKE_C_PREPROCESSOR "${TOOLCHAIN_PATH_AND_PREFIX}-cpp"     CACHE PATH "Preprocessor" )
   set(CMAKE_STRIP          "${TOOLCHAIN_PATH_AND_PREFIX}-strip"   CACHE PATH "strip" )
   if( EXISTS "${TOOLCHAIN_PATH_AND_PREFIX}-gcc-ar" )
   # Prefer gcc-ar over binutils ar: https://gcc.gnu.org/wiki/LinkTimeOptimizationFAQ
   set(CMAKE_AR           "${TOOLCHAIN_PATH_AND_PREFIX}-gcc-ar"  CACHE PATH "Archiver" )
   else()
   set(CMAKE_AR           "${TOOLCHAIN_PATH_AND_PREFIX}-ar"      CACHE PATH "Archiver" )
   endif()
   set(CMAKE_LINKER         "${TOOLCHAIN_PATH_AND_PREFIX}-ld"      CACHE PATH "Linker" )
   set(CMAKE_NM             "${TOOLCHAIN_PATH_AND_PREFIX}-nm"      CACHE PATH "nm" )
   set(CMAKE_OBJCOPY        "${TOOLCHAIN_PATH_AND_PREFIX}-objcopy" CACHE PATH "objcopy" )
   set(CMAKE_OBJDUMP        "${TOOLCHAIN_PATH_AND_PREFIX}-objdump" CACHE PATH "objdump" )
   set(CMAKE_RANLIB         "${TOOLCHAIN_PATH_AND_PREFIX}-ranlib"  CACHE PATH "ranlib" )
   set(CMAKE_RC_COMPILER    "${TOOLCHAIN_PATH_AND_PREFIX}-windres" CACHE PATH "WindowsRC" )
   set(CMAKE_Fortran_COMPILER "${TOOLCHAIN_PATH_AND_PREFIX}-gfortran" CACHE PATH "gfortran" )
