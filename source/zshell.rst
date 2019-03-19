ZShell的使用
=========================

ZShell是一个强大的shell，它的扩展性特别好，有很多现成的配置可以使用，能够极大的
提高我们的开发效率。

安装ZShell
--------------------
.. code-block:: shell

   sudo apt-get install zsh

安装zimfw
--------------------
`zimfw` 是一个强大的ZShell的配置，大家可以参考 `它的项目主页 <https://github.com/zimfw/zimfw>`_ 来安装和配置。

快速切换目录的技巧
------------------------
.. code-block:: shell

   dirs -v
   cd +<dirno>

当我们需要在我们访问过的几个目录间来回切换的时候，先用 `dirs -v` 来查看目录栈，再用 `cd +<dirno>` 来切换目录。
其中 `dirno` 是目录栈中最左边一列显示的编号。
