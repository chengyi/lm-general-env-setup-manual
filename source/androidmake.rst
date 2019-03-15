Android开发环境简说
===========================

``AOSP`` 使用Makefile构建了一套自己的编译系统，其中有很多预定义的函数，编写Android.mk
就是使用这些预定义的函数并结合MakeFile的规则来告诉 ``AOSP`` 怎么去构建我们怎么的
代码。

例子
---------------------------
.. code-block:: makefile

   LOCAL_PATH := $(call my-dir)

   include $(CLEAR_VARS)
   LOCAL_THIRD_LDLIBS_PATH = src/third_party_libs/lib
   LOCAL_MODULE := avfilter
   LOCAL_C_INCLUDES := src/third_party_libs/include
   LOCAL_SRC_FILES := $(LOCAL_THIRD_LDLIBS_PATH)/libavfilter.a
   include $(PREBUILT_STATIC_LIBRARY)


   include $(CLEAR_VARS)
   LOCAL_SRC_FILES := src/helloworld.c

   LOCAL_MODULE := helloworld
   LOCAL_STATIC_LIBRARIES := avfilter
   LOCAL_LDLIBS += -lm -lz -lc
   include $(BUILD_SHARED_LIBRARY)

上面的例子什么意思？
---------------------------
* ``LOCAL_PATH`` 用来表示当前Android.mk所处的路径
* ``include $(CLEAR_VARS)`` 用来清空除 ``LOCAL_PATH`` 以外的变量
* ``LOCAL_THIRD_LDLIBS_PATH`` 用来表示第三方库的存放路径
* ``LOCAL_C_INCLUDES`` 用来指定编译时使用的头文件的路径
* ``LOCAL_SRC_FILES`` 用来指定编译时使用的源文件的路径
* ``include $(PREBUILT_STATIC_LIBRARY)`` 用来告诉构建系统我们希望构建一个包含预构建静态库的模块
* ``LOCAL_STATIC_LIBRARIES`` 用来指定编译所需要的静态库
* ``LOCAL_LDLIBS`` 用来指定链接使用的库
* ``BUILD_SHARED_LIBRARY`` 用来告诉编译系统我们希望构建一个共享库

上面的例子首先定义了一个 ``avfilter`` 模块。这个模块包含了一个构建好的静态库。然后
又定义了一个 ``helloworld`` 模块。 ``helloworld`` 模块编译了 ``src/helloworld.c``,
并链接了 ``avfilter`` 模块提供的静态库，同时还链接了 ``libm`` 、 ``libc`` 、 ``libz``
这些库。限于篇幅，本教程不能够涵盖Android.mk编写的全部知识，读者可以通过访问 `Android.mk <http://android.mk>`_ 来了解细节。

AOSP的编译
---------------------------
.. code-block:: console

   source build/envsetup.sh
   lunch <product-id>
   make

.. note::
   编译工作应当在 ``bash`` 下进行，因为一部分脚本不兼容其它shell。另 `一些编译指令 <https://elinux.org/Android_Build_System>`_
   也应该在 `source build/envsetup.sh` 后才能使用。
