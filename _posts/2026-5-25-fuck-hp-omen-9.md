---
layout: post
title: 爆改暗影精灵 9
---
[世人苦 OMEN Gaming Hub 久矣](https://www.bilibili.com/video/BV1nGr9YfEAU/)，而我已独善其身。于是他们命我在此书写救世的篇章，我便来了。

[这里]({% post_url 2024-8-12-reinstall-windows-twice %})是前传。我看这 8499 未尝不能和 13999 掰掰手腕。

## 先来做些前戏

本页博客所有内容均基于以下前提：

- 系统型号：OMEN by HP Gaming Laptop 16-wf0xxx
- 处理器：13th Gen Intel(R) Core(TM) i9-13900HX
- 独立显卡：NVIDIA GeForce RTX 4060 Laptop GPU
- BIOS 版本/日期：Insyde F.28, 2025/7/16
- Windows 版本：Windows 11 专业版 24H2 26100.4770
- 基于虛拟化的安全性 (VBS)：未启用
- Hyper-V：未启用
- 处理器/独立显卡导热材料：霍尼韦尔 7950 相变片
- 内存条：京东自营买的英睿达 16G x2 而非原厂海力士 8G x2

你可能需要提前准备的文件：

- [OmenSuperHub 1.9.18.0](https://github.com/Rbqwow/rbqwow.github.io/releases/download/2026-05-25/omensuperhub-win64-v1.9.18.0.zip)
- [ThrottleStop](https://github.com/Rbqwow/rbqwow.github.io/releases/download/2026-05-25/ThrottleStop_9.7.3.zip)
- [MSI Afterburner](https://github.com/Rbqwow/rbqwow.github.io/releases/download/2026-05-25/MSIAfterburnerSetup467Beta2Build16935.zip)
- [Rivatuner Statistics Server (RTSS)](https://github.com/Rbqwow/rbqwow.github.io/releases/download/2026-05-25/RTSSSetup737Build28314.zip)（好像用不上，而且 MSI Afterburner 内置了）
- [处理器调度 PPKG 预配包](https://github.com/Rbqwow/rbqwow.github.io/releases/download/2026-05-25/PPKG.zip)
- [BIOS 解锁工具](https://github.com/Rbqwow/rbqwow.github.io/releases/download/2026-05-25/omen-bios-unlock-2025-04-01.zip)
- [图吧工具箱](https://github.com/Rbqwow/rbqwow.github.io/releases/download/2026-05-25/tuba-toolbox-202601-portable.7z)
- 一个 FAT32 格式的 U 盘或其他存储介质，无需格式化（这算文件吗？）

你可能需要提前知道的事情：

- 各电脑体质不同，本文中的配置仅在我个人电脑上测试通过，仅供参考。你也可以去看看文末[参考资料](#参考资料及鸣谢)中的教程，那里的参数应该更普适一点。
- 关掉 Windows Defender 实时防护之类的安全软件，如果你信任我。在完成这页博客的所有操作之前，不要开启。  
   如果在本文全部操作结束后开启安全软件，持久化保留的文件报毒了，那就添加信任。  
   如果你不信任我，那大可关闭页面，我不会责怪你的。
- 记住自己的 BitLocker 恢复密钥，因为需要短暂关闭安全启动，之后你可以开回来。不要试图只暂停加密而不记住密钥，你会后悔的。未开启 BitLocker 则跳过这一条。
- Hyper-V 可能导致 ThrottleStop 等工具无法正常变更处理器电压和频率，WSL2 用户有难了。
- 暗影精灵 9 重置 BIOS 的方式是在内置键盘上按住 Win+V 后短按电源按钮开机，之后不要松开 Win+V，等它亮。

我的碎碎念：

- 最好通过微软官方镜像（MSDN 或其他什么）格式化系统盘重装一次，以移除所有 HP/OMEN 等 OEM 组件，因为它们全都不再被需要。你不再需要使用 OGH，也从来都不需要从 HP 处获取驱动。
- 我认为 OmenSuperHub 1.9.18.0 之后的版本稳定性欠佳，所以我推荐你使用这个版本，我提供的也是这个版本。当然新版本也一样可以用。
- 如果你因为各种原因不想动 BIOS 的话，那只看 OSH 部分也可以。它可以独立替代 OGH 工作，同样会有很大提升。
- 推荐使用 HWiNFO 长期监控电压、温度等数据。当然你只用刚刚那些工具看也不是不行，只是我更喜欢它。
- 什么你说内存超频，我换英睿达了超不了一超就黑，原厂海力士倒是可以抄抄参考资料里的作业。
- 我不太建议你问 LLM 有关这篇博客的事，因为即将发生的事很小众变态，我认为 LLM 不太能理解。总之小心。

如果你都理解了，那么我们开始。

## 在修改 BIOS 之前

1. 让你的 OGH 等 HP/OMEN 组件消失或全部不再运行。

   我通过微软官方镜像重装了系统，所以我没办法针对 OEM 系统给出太多意见。  
   我已知的自启方式有用户启动项、系统启动项、任务计划、服务、UWP 应用自启。总之所有能自启的地方都有 HP/OMEN 的身影。你加油。

2. 安装处理器调度 PPKG 预配包，它为 Windows 提供了适合的处理器调度策略。

   装不上也没什么问题，总之装一下。总之我现在装不上了。

3. 将正确的 BIOS 解锁工具文件放入 U 盘内。

   我使用的是 `暗影精灵 9 9slim`，供你参考。U 盘根目录最终应该存在 `EFI` 文件夹和 `SREP_Config.cfg` 文件。可以同时存在其他文件，没有关系。  
   记得看自己的 BIOS 版本，Win+R 输入 `msinfo32` 可以打开系统信息。

## 是时候爆草 BIOS 了

1. 准备好你的 U 盘，把它插上。
2. 开机连按 F10 进入 BIOS 设置，先重置 BIOS 设置，再关闭安全启动。按 F10 保存并退出。
3. 开机连按 F9 进入启动项选择，使用你的 U 盘启动，等候片刻。自动回到启动项选择页面后，按 F10 进入 BIOS 设置。
4. 查看 BIOS 设置首页的底部，选择多出来的 `自定义` 选项并进入。
5. 寻找以下设置，并将它们修改为对应值。 TODO：列出选项位置
   - `Overclocking Lock` → 关
   - `AC Loadline` → 110
   - `Energy Efficient Turbo` → 关
   - `Overclocking Feature` → 开
   - `Thermal Velocity Boost` → 关
   - `TVB Voltage Optimizations` → 关
   - `IA CEP` → 关
   - `Enhanced Thermal Velocity Boost` → 关
   - `UnderVolt Protection` → 关
6. 按 F10 保存并退出。

U 盘启动或否只会影响隐藏设置解锁，并不会影响已经修改的设置项。如修改独显等设置可以正常进入修改。

## 现在是正戏时间

先找个不需要特殊权限的文件夹，就是那种新建删除修改重命名文件都不会额外弹窗找你确认管理员权限的文件夹，把 OmenSuperHub 和 ThrottleStop 两个 EXE 解压进去。

### OmenSuperHub

安装 PawnIO 后启动 OmenSuperHub，该软件没有图形界面，你可以在任务托盘中找到它。

初次运行 OSH 时会弹出关于页面，其中包含软件功能介绍。OSH 很简单，在你完整阅读说明后应该就能掌握。

我的自用配置和理由：

- `风扇配置` → 中  
  我认为默认的 `高` 调度速度太过激进，经常出现拉风箱的情况，而中会好很多。
- `性能控制` → 狂暴模式  
  通过下面的 CPU 功率限制调整功耗已经足够，不要让事情变复杂。
- `性能控制` → CTGP开+DB开 / `切换DB版本` → 解锁版本  
  等到用的时候再切解锁就晚了。
- `性能控制` - `CPU功率` → 60W  
  60 瓦是一个甜点区，不会很卡，也不会呼呼转，日用我很喜欢。打游戏时会凭直觉切到 80 / 100 瓦。
- `性能控制` - `GPU频率限制` → 还原  
  把这个交给 MSI Afterburner 吧。
- `浮窗显示` → 显示浮窗 / 36号 / 左上角  
- `Omen键` → `切换浮窗显示`  
  这浮窗真帅吧，字体小一点感觉更好。绑到 OGH 的遗产上正好可以在挡住东西时快速切换开关。
- `其他设置` - `开机自启` → 开启  
  ∵ OGH 开机自启。
  ∵ OSH 替代了 OGH。
  ∴ OSH 需要开机自启。
  // Q.E.D.

自用风扇调度参数：

`cool.txt`

```csv
CPU,Fan1,Fan2,GPU,Fan1,Fan2
65,2000,2000,54,2000,2000
75,3000,3000,64,3000,3000
85,4400,4400,74,4400,4400
95,6400,6400,84,6400,6400
```

`silent.txt`

```csv
CPU,Fan1,Fan2,GPU,Fan1,Fan2
65,2000,2000,54,2000,2000
75,2000,2000,64,2000,2000
85,2800,2800,74,2800,2800
95,4000,4000,84,4000,4000
```

### ThrottleStop

启动 ThrottleStop，点击 `Turn On`，再记住**每步结束后点击 `Save` 或 `Apply`**，然后……

1. 左上角选择第一个配置 `Performance`，
   - `Speed Shift EPP` → 启用、0
   - `Speedstep` → 启用
   - `C1E` → 启用

   左上角选择第二个配置 `Game`，
   - `Speed Shift EPP` → 启用、32
   - `Speedstep` → 启用
   - `C1E` → 启用

   左上角选择第三个配置 `Internet`，
   - `Speed Shift EPP` → 启用、128
   - `Speedstep` → 启用
   - `C1E` → 启用

   左上角选择第四个配置 `Battery`，
   - `Speed Shift EPP` → 启用、255
   - `Speedstep` → 启用
   - `C1E` → 启用

2. 点击 `FIVR`，进入电压调节设置界面，右下角选择 `OK - Save voltages immediately.`，

   左上角选择第一个配置 `Performance`，
   - `Performance Cores` - `Turbo Groups` - `Ratios` - `Group <0-7>` → 50
   - `Efficiency Cores` - `Turbo Groups` - `Ratios` - `Group 0` → 33
   - `FIVR Control` - `CPU Core` / `CPU P Cache` / `CPU E Cache` - `Unlock Adjustable Voltage` → 启用
   - `FIVR Control` - `CPU Core` / `CPU P Cache` / `CPU E Cache` - `Offset Voltage` → -99.6
   - `FIVR Control` - `CPU Core` - `IccMax` → 255.75
   - `Cache Ratio` - `Min / Max` → 8 / 41
   - `Miscellaneous` - `V-Max Stress` / `Ring Down Bin` / `Overclock` → 启用

   左上角选择第二个配置 `Game`，
   - `Performance Cores` - `Turbo Groups` - `Ratios` - `Group <0-7>` → 48
   - `Efficiency Cores` - `Turbo Groups` - `Ratios` - `Group 0` → 32
   - `FIVR Control` - `CPU Core` / `CPU P Cache` / `CPU E Cache` - `Unlock Adjustable Voltage` → 启用
   - `FIVR Control` - `CPU Core` / `CPU P Cache` / `CPU E Cache` - `Offset Voltage` → -89.8
   - `FIVR Control` - `CPU Core` - `IccMax` → 255.75
   - `Cache Ratio` - `Min / Max` → 8 / 39
   - `Miscellaneous` - `V-Max Stress` / `Ring Down Bin` / `Overclock` → 启用

   左上角选择第三个配置 `Internet`，
   - `Performance Cores` - `Turbo Groups` - `Ratios` - `Group <0-7>` → 44
   - `Efficiency Cores` - `Turbo Groups` - `Ratios` - `Group 0` → 30
   - `FIVR Control` - `CPU Core` / `CPU P Cache` / `CPU E Cache` - `Unlock Adjustable Voltage` → 启用
   - `FIVR Control` - `CPU Core` / `CPU P Cache` / `CPU E Cache` - `Offset Voltage` → -99.6
   - `FIVR Control` - `CPU Core` - `IccMax` → 255.75
   - `Cache Ratio` - `Min / Max` → 8 / 37
   - `Miscellaneous` - `V-Max Stress` / `Ring Down Bin` → 启用
   - `Miscellaneous` - `Overclock` → 禁用

   左上角选择第四个配置 `Battery`，
   - `Performance Cores` - `Turbo Groups` - `Ratios` - `Group <0-1>` → 40
   - `Performance Cores` - `Turbo Groups` - `Ratios` - `Group <2-3>` → 38
   - `Performance Cores` - `Turbo Groups` - `Ratios` - `Group <4-5>` → 36
   - `Performance Cores` - `Turbo Groups` - `Ratios` - `Group <6-7>` → 35
   - `Efficiency Cores` - `Turbo Groups` - `Ratios` - `Group 0` → 28
   - `Cache Ratio` - `Min / Max` → 8 / 35
   - `Miscellaneous` - `Ring Down Bin` → 启用
   - `Miscellaneous` - `V-Max Stress` / `Overclock` → 禁用

3. 回到主页面，点击 `Options`，进入主设置界面，

   右上角 `Default Profiles`，
   - `AC Profile` → 启用、1
   - `Battery Profile` → 启用、4
   - `Low Battery %` → 0

   右侧 `Miscellaneous`，
   - `Battery Monitoring` → 启用
   - `Start Minimized` → 启用
   - `Minimize on Close` → 启用
   - `Nvidia GPU` → 启用

4. 通过任务计划设置开机自启。

   Win+R 输入 `taskschd.msc` 打开任务计划程序，选中右侧 `任务计划程序库`，右键单击，选择 `创建任务(R)...`，填写任务参数：

   1. `常规`
      - `名称(M)` → ThrottleStop
      - `使用最高权限运行(I)` → 启用
   2. `触发器`
      - `新建(N)...` - `开始任务(G):` → 登录时 → 确定
   3. `操作`
      - `新建(N)...` - `操作(I):` → 启动程序
      - `设置` - `程序或脚本(P):` - `浏览(R)...` → 选择 ThrottleStop.exe → 确定
   4. `条件`
      - 清除所有复选框
   5. `设置`
      - 清除除 `允许按需运行任务(L)` 以外的所有复选框

### MSI Afterburner

安装 MSI Afterburner。没错，这个需要安装了。

安装成功后运行它，然后……

1. 调整频率
   - `CLOCK` - `CORE CLOCK` → +255
   - `CLOCK` - `MEMORY CLOCK` → +510

2. 调整电压/频率曲线
   1. 点击 `VOLTAGE` - `CURVE EDITOR` 进入电压/频率曲线编辑器
   2. 在横轴上找到 `925`，找到曲线上对应的那个点，再向左一个点。
   3. 一直按住 Shift 键，从刚刚找到的点开始，长按鼠标左键向右拖拽，选中右侧的全部点。
   4. 一直按住 Shift 键，点击刚刚找到的点。
   5. 一直按住 Shift 键，双击回车。
   6. 松开 Shift 键。

   如果选中的点变成一条直线则结束，如果操作失误没有成功，则点击底部保存按钮左侧的恢复按钮重试。

3. 应用、保存配置，并配置开机自启
   1. 点击底部保存按钮右侧的对勾按钮。
   2. 点击底部保存按钮。
   3. 点击右侧配置 1 按钮。
   4. 点击右上角 Windows 徽标按钮。
   5. 点击右侧锁按钮。

需要注意，MSI Afterburner 和 Rivatuner Statistics Server 都是点击最小化会最小化到任务托盘，点击关闭会直接退出。

### 结语

至此，暗影精灵 9 爆改完成咯，恭喜你获得了一台双烤 130 瓦 + 140 瓦 的暗影精灵，280 瓦的电源适配器都吓哭了。

你可以使用图吧工具箱中的 `P95一键烤机` 来检验处理器稳定性，使用 `FurMark2` 来检验独立显卡稳定性，亦或同时运行二者，尽情体验不一样的暗影精灵吧。

博客内容可能不够完善，尚待补充，欢迎提出意见或建议，或者单纯提问，说不定就进入常见问题了呢。

### 常见问题

1. 我因为各种理由总之是重置了 BIOS，现在应该补充做哪步？

   只需要重新[修改 BIOS 配置](#是时候爆草-bios-了)即可。

2. 烤机蓝屏 / 游戏崩溃了怎么办？

   先看散热。我在更换 7950 相变片的同时还在使用压风散热器，虽然还是压不住整机持续 270 瓦，但一年后持续 240 瓦还是随随便便压的。

   如果散热没有明显问题，那大概率是处理器的问题。我在使用 Performance 档位双烤测试时会因处理器缓存蓝屏，或因独立显卡 PCIe 错误导致进程崩溃。单烤处理器不会，使用 Game 档位双烤也不会。所以我没有再调整。你可以试着多给一些电压，尤其是缓存电压。

   或者去抄一下参考资料里的配置作业，那里面的配置更保守一些。

## 参考资料及鸣谢

- [暗影精灵9重生满血功耗教程（哔哩哔哩）](https://www.bilibili.com/video/BV1jK4aeVEwN/)
- [OmenSuperHub (GitHub)](https://github.com/breadeding/OmenSuperHub)
- 所有制作了本博客提供的文件的人

感谢所有前人们的探索以根治惠普引以为傲的大厂病。
