对于Linux的操作，一直是野路子出身，没有系统学习过，所以最近在看《Linux命令行大全》来系统学习。这本书的第三章《Linux系统》有一部分是解释Linux中各种目录的作用的。这些目录的名称大多都是缩写，但其究竟是什么的缩写和其来由都没有在本书说明，因此我在网上搜索整理的这份文档，以备查阅。

* /bin - **Bin**aries.
* /boot - Files required for **boot**ing.
* /dev - **Dev**ice files.
* /etc - **Et** **c**etera. The name is inherited from the earliest Unixes, which is when it became the spot to put config-files.
* /home - Where **home** directories are kept.
* /lib - Where code **lib**raries are kept.
* /media - A more modern directory, but where removable **media** gets mounted.
* /mnt - Where temporary file-systems are **m**ou**nt**ed.
* /opt - Where **opt**ional add-on software is installed. This is discrete from /usr/local/ for reasons I'll get to later.
* /run - Where **run**time variable data is kept.
* **/sbin** - Where **s**uper-**bin**aries are stored. These usually only work with root.
* /srv - Stands for "**s**e**rv**e". This directory is intended for static files that are served out. /srv/http would be for static websites, /srv/ftp for an FTP server.
* /tmp - Where **t**e**mp**orary files may be stored.
* **/usr** - Another directory inherited from the Unixes of old, it stands for "**U**NIX **S**ystem **R**esources". It does not stand for "user" (see the Debian Wiki). This directory should be sharable between hosts, and can be NFS mounted to multiple hosts safely. It can be mounted read-only safely.
* /var - Another directory inherited from the Unixes of old, it stands for "**var**iable". This is where system data that varies may be stored. Such things as spool and cache directories may be located here. If a program needs to write to the local file-system and isn't serving that data to someone directly, it'll go here.
 * /proc - Kernel and **proc**ess information virtual filesystem. Read it to get the process information as well as other kernel and memory information.


> 参考资料：
> * [linux目录名含义](https://blog.csdn.net/Mr_Cat123/article/details/103346618)
> * [Meaning of directories on Unix and Unix like systems](https://serverfault.com/questions/24523/meaning-of-directories-on-unix-and-unix-like-systems)
> * [**\*Linux Filesystem Hierarchy Standard 3.0**](https://refspecs.linuxfoundation.org/FHS_3.0/fhs/index.html)
