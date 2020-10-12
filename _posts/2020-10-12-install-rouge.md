---
title: 安装Rouge1.5.5
tags: 杂项 NLP ROUGE pyrouge
---

Rouge的安装向来很麻烦，这里给出在Ubuntu 16.04+和Windows 10两个系统下安装Rouge 1.5.5的简明步骤。

## Ubuntu

1. Ubuntu一般自带perl，bash输入`perl -v`检查perl版本
2. 输入`cpan -v`检查cpan（perl的包管理器）的版本，初次使用会提示需要配置，一路Enter即可
3. 使用`sudo cpan install XML::DOM`安装必须的XML解析包
4. 下载[ROUGE-1.5.5](https://github.com/andersjo/pyrouge/tree/master/tools/ROUGE-1.5.5)，该资源在网上随处可见，**与操作系统无关**，下载后运行目录下的perl脚本`runROUGE-test.pl`，若它正常运行不报错则一切OK

## Windows

>  下面是在Windows下安装Rouge和 [pyrouge](https://github.com/bheinzerling/pyrouge) 的指南，引用自https://www.jianshu.com/p/cb48ec5eb123，还不行的话参考[这个Stackoverflow问题](https://stackoverflow.com/questions/47045436/how-to-install-the-python-package-pyrouge-on-microsoft-windows/47045437#47045437)

1. 安装Windows版[Perl](https://links.jianshu.com/go?to=http%3A%2F%2Fstrawberryperl.com%2F)
2. 安装perl库XML::DOM：运行Strawberry Perl 中的运行组件CPAN Client，输入`install XML::DOM`
3. 下载 [ROUGE-1.5.5](https://github.com/summanlp/evaluation/tree/master/ROUGE-RELEASE-1.5.5)。 并将`path\to\RELEASE-1.5.5`和`path\to\RELEASE-1.5.5\data`添加到环境变量中
4. 安装pyrouge：`pip install pyrouge`
5. 参考pyrouge项目的README使用ROUGE
6. 如果遇到[WindowsError:\[Error 193\] %1 is not a valid Win32 application in Python](https://stackoverflow.com/questions/15374710/windowserror-error-193-1-is-not-a-valid-win32-application-in-python) ，参考https://github.com/Tencent/mars/issues/377  下载了libeay32.dll的32位版本，解压拷贝到System32 和SysWoW64目录下，就可以了
