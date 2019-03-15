阅读代码
=======================

说实话， Linux下的代码阅读工具主要是各种Tag系统，在这里，介绍下我使用的 `GNU Global`.

安装GNU Global
----------------------
.. code-block:: shell

   sudo apt-get install global
   sudo apt-get install ctags

.. hint::
   如果你嫌Ubuntu源里自带的版本太旧， 可以前往 `Getting GLOBAL <https://www.gnu.org/software/global/download.html>`_ 下载源码自己编译。
   同时你还可以编译 `universal-ctags <https://github.com/universal-ctags/ctags>`_ 来取代ctags, 以提高查找的精确度。

GNU Global的使用
----------------------
1. 在你的源码目录下面运行 `gtags -v` 来索引源码。

   .. hint::
      如果你索引C++代码， 运行 `export GTAGSFORCECPP=1` 使得global将 `.h` 文件当作C++文件。

2. 索引结束后你就可以使用 `global` 来浏览代码了。常用命令如下：

   ==================== ===========================
   global <symbol>      查看symbol在什么地方被定义
   global -xr <symbol>  查看symbol在什么地方被使用
   global -u            增量更新tags
   ==================== ===========================

   .. hint::
      通常可以将tag工具通过插件和编辑器更好的结合起来，不过限于篇幅，本教程并不打算
      涉及这个主题。读者可以自行研究。
