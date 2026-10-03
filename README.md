一份对Windows游戏的丝滑度、手感等因素的研究心得  

## 系统版本建议
请查看(https://learn.microsoft.com/windows-hardware/design/minimum/windows-processor-requirements)  
然后按照自己的CPU最高可用的Windows版本选择  

## 系统设置选项建议
| 系统版本 | 硬件加速GPU计划 | 窗口化游戏优化 |
| :------- | :-------------- | :------------- |
| <=23H2   | 不开            | 不开           |
| >=24H2   | 开              | 开             |

## 驱动建议
- 新显卡的驱动需要定期更新，老显卡用系统自动装的驱动就是最稳定  
- NV显卡追求最新特性和最新BUG修复就选择`GameReady`驱动，特别注重驱动的稳定性可以选择`Studio`驱动  
- `网卡驱动`和`芯片组驱动`不要太老就好  

## 显示器建议
- 一定要注意显卡`DP`和`HDMI`接口版本和显示器接口版本是不是对等  
  其次如果要保证延迟稳定性，线材最长不超过`1.5m`  
  然后不要被显示器分辨率迷惑，4K高刷如果DP接口只有`DP1.4`，就必须开`DSC`使用  
  开`DSC`要付出很多代价，不开就没法用高刷  

- 用DP接口需要手动在显示器开启`FreeSync`/`AdaptiveSync`，HDMI接口是自动识别`VRR`  
  前提是支持这种`可变垂直同步`技术的显示器  
  目前以我的使用经验下来，NV显卡更偏好HDMI的`VRR`，AMD显卡更偏好DP的`FreeSync`  

- 非到必要，不选择超频档刷新率，只选择原生刷新率  
  超频档刷新率会导致显存无法休眠，永远在最高频，增加大量待机功耗  

- 进阶操作可以使用[CRU](https://github.com/kreier/cru)工具创建一个更稳的刷新率  
  计时标准选择`Automatic PC`，这个是消隐兼容性最好的计时  
  更稳的刷新率意思是把刷新率调整到`像素频率`不超过显示器面板带载上限  
  注意`DSC`技术和自定义刷新率不兼容，但是`FreeSync`会很好补缺这块短板  

## 锁定ISR到CPU0
把ISR锁定到CPU0处理，可以防止ISR轮流到不同CPU核心反复重新调度的延迟，尤其是鼠标的ISR收益会更大  
- 修改方法  
  下载和管理员运行[set-isr-cpu0.bat](./bin/set-isr-cpu0.bat)  

然后把CPU0的超线程弃用调度，同时还能阻止系统插手节能CPU0，让ISR永远低延迟处理  
- 修改方法  
  下载和管理员运行[set-isr-cpu0-reserved.bat](./bin/set-isr-cpu0-reserved.bat)  

> [!NOTE]  
> 对于没有超线程的CPU，不需要运行这个  

建议也把tick中断来源设置为强制LAPIC，确保阻止系统意外调用主板的慢速tick  
- 修改方法  
  下载和管理员运行[set-isr-lapic.bat](./bin/set-isr-lapic.bat)  

重置修改  
- 下载和管理员运行[reset-isr-default.bat](./bin/reset-isr-default.bat)  

> [!TIP]  
> 建议把鼠标设备插入到主板上能和CPU直连的PCIe链路上  
> 可以免去走芯片组中继  

> [!IMPORTANT]  
> 需要重启生效  

> [!NOTE]  
> 运行一次即整个系统永久保持，不需要加入开机自启  

祝AMD用户摆脱鼠标粘滞感  

## 关闭MPO
系统的MPO功能会在游戏高GPU负载时影响低GPU负载程序的画面更新，出现画面残留  
关闭这个功能可以缓解  

[set-mpo-disabled.bat ]: ./bin/set-mpo-disabled.bat
[reset-mpo-default.bat]: ./bin/reset-mpo-default.bat

- 修改方法  
  下载和管理员运行[set-mpo-disabled.bat]  
- 重置修改  
  下载和管理员运行[reset-mpo-default.bat]  

> [!IMPORTANT]  
> 需要重启生效  

> [!NOTE]  
> 运行一次即整个系统永久保持，不需要加入开机自启  

## 全局计时器精度
重新开启全局同步的最高计时器精度，可以让每个程序的中断计时器获得同等的最高精度  

[set-globaltimeres-enabled.bat  ]: ./bin/set-globaltimeres-enabled.bat
[reset-globaltimeres-default.bat]: ./bin/reset-globaltimeres-default.bat

- 修改方法  
  下载和管理员运行[set-globaltimeres-enabled.bat]  
- 重置修改  
  下载和管理员运行[reset-globaltimeres-default.bat]  

> [!IMPORTANT]  
> 需要重启生效  

> [!NOTE]  
> 运行一次即整个系统永久保持，不需要加入开机自启  

## 关闭鼠标增强指针精度
这是一个大幅影响日常办公鼠标手感的修改  
如果要玩的游戏不支持`原始鼠标输入`，这个修改就可以消除鼠标加速带来的游戏里的奇怪鼠标手感  

[set-mouseacceleration-off.bat      ]: ./bin/set-mouseacceleration-off.bat
[reset-mouseacceleration-default.bat]: ./bin/reset-mouseacceleration-default.bat

- 修改方法  
  下载和管理员运行[set-mouseacceleration-off.bat]  
- 重置修改  
  下载和管理员运行[reset-mouseacceleration-default.bat]  

> [!IMPORTANT]  
> 需要重启生效  

> [!NOTE]  
> 运行一次即整个系统永久保持，不需要加入开机自启  

## 修改前后台时间片分配
这是一个细微影响鼠标手感的修改，推荐FPS选手  

[set-fgbgslice-fix31.bat    ]: ./bin/set-fgbgslice-fix31.bat
[set-fgbgslice-var31.bat    ]: ./bin/set-fgbgslice-var31.bat
[reset-fgbgslice-default.bat]: ./bin/reset-fgbgslice-default.bat

- 修改方法  
  - `低灵敏度玩家`：下载和管理员运行[set-fgbgslice-fix31.bat]  
  - `高灵敏度玩家`：下载和管理员运行[set-fgbgslice-var31.bat]  
- 重置修改  
  下载和管理员运行[reset-fgbgslice-default.bat]  

> [!IMPORTANT]  
> 需要重启生效  

> [!NOTE]  
> 运行一次即整个系统永久保持，不需要加入开机自启  

<details>
<summary>Win32PrioritySeparation二进制位解释</summary>

|          | 6~5位      | 4~3位      | 2~1位            |
| :------- | :--------- | :--------- | :--------------- |
| 解释     | 时间片长短 | 长短可变性 | 前后台时间片比例 |
| 数值作用 | 00 = 默认  | 00 = 默认  | 00 = 1:1         |
| 数值作用 | 01 = 长    | 01 = 可变  | 01 = 2:1         |
| 数值作用 | 10 = 短    | 10 = 固定  | 10 = 3:1         |
| 数值作用 | 11 = 默认  | 10 = 默认  | 11 = 3:1         |

举例：
- 二进制`010110`表示`可变长3:1`调度，对应十六进制`16`，十进制`22`
- 二进制`101010`表示`固定短3:1`调度，对应十六进制`2a`，十进制`42`

</details>
