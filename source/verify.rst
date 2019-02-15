验证你的ISO镜像
=====================

验证ISO镜像 的完整性和可靠性是很重要的。

完整性检查确认你的ISO映像是否被正确下载，并且你的本地文件是否和下载服务器上存在的文件完全一致。下载过程中的错误可能导致文件损坏，并在安装过程中引发随机问题。

可靠性检验确认你下载的ISO镜像是否由Linux Mint签署的，因此它并不是被别人修改或恶意的拷贝。

下载由Linux Mint提供的SHA256值
-----------------------------------------------

所有 `download mirrors <https://www.linuxmint.com/mirrors.php>`_ 都提供ISO镜像, 一个 ``sha256sum.txt`` 文件和一个 ``sha256sum.txt.gpg`` 文件。你应该能够在你下载ISO镜像相同的位置找到这些文件。

如果你不能找到他们，请浏览 `Heanet download mirror <https://ftp.heanet.ie/mirrors/linuxmint.com/stable/>`_ 并且检查你下载的Linux Mint版本。

下载 ``sha256sum.txt`` 和 ``sha256sum.txt.gpg``.

完整性检查
---------------

为了检验你本地ISO文件的完整性，生成它的SHA256值并且与 ``sha256sum.txt`` 里面的值进行比较。

.. code-block:: console

    sha256sum -b yourfile.iso

.. hint::
   如果你正在使用Windows系统，你可以通过安装 `Cygwin <http://www.cygwin.com/>`_ 得到sha256值（和gpg）命令工具。

如果sum匹配，你的ISO映像被成功下载。如果没有，请再次下载。

`````

校验和真实性检查
------------------

采取下面的步骤来核实 ``sha256sum.txt`` 的真实性, 以及检查 ``sha256sum.txt.gpg`` 的签名。

导入的Linux Mint的签名密钥：
``````````````````````````````````
.. code-block:: console

   gpg --keyserver keyserver.ubuntu.com --recv-key "27DE B156 44C6 B3CF 3BD7  D291 300F 846B A25B AE09"

.. note::
    如果gpg提示ID非法，请尝试下面的命令：

    .. code-block:: console

        gpg --keyserver keyserver.ubuntu.com --recv-key A25BAE09
        gpg --list-key --with-fingerprint A25BAE09

    检查最后命令的输出，确保指纹是 ``27DE B156 44C6 B3CF 3BD7 D291 300F 846B A25B AE09``.

验证sha256sum.txt的真实性：
`````````````````````````````````````````
.. code-block:: console

    gpg --verify sha256sum.txt.gpg sha256sum.txt

最后指令的输出应该告诉你文件签名是 ``good`` 的而且它以 ``A25BAE09`` 密钥签署。

.. note::
    GPG也许会警告你Linux Mint签名并不会你的电脑所信任。这是意料之中的，非常正常。

.. hint::
   寻找有关ISO校验的更多信息，或者BETA、LMDE以及其他发行版校验的信息，请阅读 `How to Verify ISO images <https://linuxmint.com/verify.php>`_ 。
