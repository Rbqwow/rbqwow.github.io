---
layout: post
title: 记两次重装系统
---
于是没过两个月，我又重装了一次。感谢打印机，就当是提前体验 24H2 了。

2024.8.11 - 2024.10.5

## 老生常谈的备份

记得备份了的：

- 配置：OBS、qBittorrent、Proxifier、PixPin、PotPlayer、madVR、RustDesk、BitLocker、BetterNCM、Sayo 外设、Chrome 扩展（Adguard、Ublock、篡改猴、Ublacklist）、火绒自定义保护、工程文件、Scoop 和 WinGet 列表
- 文件：桌面上的、下载文件夹里的、显卡驱动、键盘鼠标驱动

忘记备份了的：GPG 私钥、微星小飞机和 RTSS 的参数、AppData 下的某些东西 ~~比如把存档放在里面的厨圣~~

没事，不用担心，第二次重装系统是参照这篇文章做的，一件不落。

另外备份 Scoop 和 WinGet 列表真的是太有用了，完美解决“我都装了些什么”的问题。

## 释放映像

不要觉得自己会了就手动用 DISM 之类的东西释放映像，老老实实让它自己分区自己装。

真的会后悔的，别问为什么。

不过你如果真觉得你比我会也可以试试，万一我在乎的点你不在乎呢。

当然也不排除是我真的菜。

## 系统优化

现代处理器显卡硬盘内存什么的根本不差那点性能，别总惦记那个什么一键往死里优化之类的了。

总结一句话就是，不要做你不知道的事情，不要动你不明白的东西。不要乱动，屎山将倾可是会砸死人的。

## 系统还原

这东西也算是老生常谈的褒贬不一了，我觉得这东西挺有用的，至少它救了我好几次。

在以前的旧电脑上你说把它关了能怎么怎么样我也许会苟同，但现在这些电脑，参照上条所说的，真不至于。

这东西也是实打实能救命的，至少对于我这种看不惯“小问题”的用户来说。

## 微星小飞机和 RTSS 配置路径

- `C:\Program Files (x86)\MSI Afterburner\Profiles`
- `C:\Program Files (x86)\RivaTuner Statistics Server\Profiles`

~~该说不愧是一家的东西吗~~

## 笔记本

### 核显

我平时是在 BIOS 里把核显屏蔽掉用纯独显的，既为了让外接显示器时内置显示器能走独显，也为了减少些 CPU 不必要的功耗。

离电续航刚刚好一个半小时一节课，非常完美。

重装系统时得记得提前改回默认，让系统给核显打个驱动。万一什么时候突然就用到了，现打驱动显然来不及。

比如通过 Windows Update 更新下 BIOS 竟然会重置 BIOS 设置，直接改回去了。

### 驱动

惠普的驱动安装程序也是真的反人类，后台静默安装没提示，装没装完都不知道，还好有 Windows Update。

相信 Windows Update，它会帮你装好的。

### OEM 软件

显然，现代笔记本都有自己的 OEM 软件，用来控制性能调度、风扇转速、灯光特效之类的。

有些能受 BIOS 控制的机型，即使没装 OEM 软件也能干个八九不离十的活。但暗影精灵 9 并不是，一旦不装 OMEN Gaming Hub，上述两者直接就开始摆烂了。

嗯，这笔记本没灯效可调。

也还好，重装完系统先等自动更新跑完，再去 Microsoft Store 里装一下 OMEN Gaming Hub，再打开它自动装一下 SDK，就结束了。

出厂系统还预装了一大堆其他的 OEM 软件，但其实都没啥用，相信 Windows Update。

但是 OMEN Gaming Hub 也是真的难用。

## Adobe

第一次重装系统全都换成 2024 版了，越用越不对劲，怎么感觉变了一堆东西，用不明白了。

于是第二次换回了 vposy 的 2023。赞美。

## 右键新建菜单多出来一个 `BMP 图像`

用 ContextMenuManager 找不到，`HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\Discardable\PostSetup\ShellNew\Classes` 和 `HKEY_CLASSES_ROOT\*\ShellNew` 里也没有。

最后在 `HKEY_CLASSES_ROOT\Local Settings\MrtCache\C:%5CProgram Files%5CWindowsApps%5CMicrosoft.Paint_<版本>\<子目录>\<子目录>` 里面，把 `@{Microsoft.Paint_<版本>? ms-resource://Microsoft.Paint/Resources/ShellNewDisplayName_Bmp}` 的数值删空，就删掉了。

有可能有好几个 `ShellNewDisplayName_Bmp`，但是实测只需要删一个就好了，需要删的应该是子项最多的那个文件夹里面的。
