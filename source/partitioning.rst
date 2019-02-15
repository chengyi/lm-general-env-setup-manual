分区
============

Linux下的磁盘和分区
--------------------------------

如果你不熟悉设备和分区的Linux命名方案，或者文件系统和挂载点的概念，请阅读：

* `A beginner’s guide to disks and disk partitions in Linux <http://linuxbsdos.com/2014/11/08/a-beginners-guide-to-disks-and-disk-partitions-in-linux/>`_
* `Device Names in Linux <https://www.debian.org/releases/wheezy/amd64/apcs04.html.en>`_
* `Understanding the Linux File System <http://etutorials.org/Linux+systems/red+hat+linux+9+professional+secrets/Part+II+Exploring+Red+Hat+Linux/Chapter+7+Red+Hat+Linux+Basics/Understanding+the+Linux+File+System/>`_

独立的/home分区
-------------------------

在Linux中， ``/home`` 目录被用来个人数据和偏好设置。

这个目录包含每个用户帐户的一个子目录。 比如说你的用户名是 ``john``, 你的主目录是 ``/home/john``, 你的下载目录是 ``/home/john/Downloads``, 你的文档目录是 ``/home/john/Documents``, 你的火狐书签在 ``/home/john/.mozilla``, 等等。

通过给 ``/home`` 单独分区, 你可以把个人数据和其余部分的操作系统分开来。

优点是您可以在不影响用户数据的情况下擦除操作系统并进行替换。

您在安装 Linux Mint 时：

1. 将 ``/`` 挂载点分配给专用于操作系统的分区，并告诉安装程序将其格式化。

2. 将 ``/home`` 挂载点分配给专用于用户数据的分区，如果它已经包含用户数据，请确保告诉安装程序 **不要格式化它**。

.. warning::
    对于新手用户来说，这是不推荐的。安装过程中的失误可能会删除所有数据。始终进行备份，确保选择了正确的分区并仔细查看格式化选项。

.. note::
    Linux Mint操作系统大约占据15GB并随着你安装的附加软件而增长。如果你能腾出空间，给它100GB。保留大部分空闲空间用于家分区。用户数据（下载、视频、图片）会占用更多的空间。
