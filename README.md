# 2019款安卓Walkman设备技术文档

## 操作指南

本指南仅适用于已掌握基本终端命令、ADB或fastboot的高级用户。需预先安装Android Platform Tools (含ADB等工具)

### <ins>免责声明</ins>

若操作导致设备变砖/损坏，本人概不负责且不接受任何投诉。不提供终端用户技术支持，不教授adb安装或基础命令执行等入门知识。

### <ins>Windows用户专备步骤</ins>:

请先完成以下操作：

1. 在Walkman开发者选项中启用USB调试
2. 将Walkman连接至Windows电脑
3. 验证adb可用性，执行`adb shell getprop ro.boot.slot_suffix`
4. 记录输出结果（应为"_a"或"_b"）
5. 下载uuu工具[点此获取](https://github.com/Sikz1218/Sony_android_walkman_A105/raw/refs/heads/main/uuu.exe)
6. 将`uuu.exe`放入工作目录，执行`uuu`验证工具可用性

命令输出结果将作为执行`uuu`命令时的分区后缀参考

### Bootloader解锁

此操作将清除所有用户数据
1. 在开发者选项中启用OEM解锁和ADB调试
2. 执行`adb reboot bootloader`进入fastboot模式
3. 在fastboot模式（SONY logo界面）执行：
    - Mac/Linux: `fastboot oem unlock`
    - Windows: `uuu FB: oem unlock`
4. 执行后设备会进入假死状态，实际正在进行用户分区擦除（约需500秒）
5. 完成后执行重启命令：
   - Mac/Linux: `fastboot reboot`
   - Windows: `uuu FB: reboot`

### 禁用AVB

使用自定义内核需执行此步骤。通过以下命令刷写空白vbmeta文件禁用AVB：
- Mac/Linux: `fastboot --disable-verity --disable-verification flash vbmeta blank_vbmeta.img`
- Windows(_a): `uuu FB: flash vbmeta_a blank_vbmeta.img`
- Windows(_b): `uuu FB: flash vbmeta_b blank_vbmeta.img`

首次启动将进入bootloop，随后进入恢复模式提示系统启动失败。此时需选择恢复出厂设置选项（使用音量键导航，电源键确认）。重置后系统应正常启动

### 内核

本仓库内核源码已添加以下补丁：
- KernelSU支持
- 低频CPU支持
- 节能型CPU调频策略
使用随附的`walkman.config`作为编译配置

预编译内核[点此下载](https://github.com/notcbw/2019_android_walkman/releases/tag/v1)。进入fastboot后执行（A100机型需修改文件名）：
- Mac/Linux: `fastboot flash boot boot-zx500.img`
- Windows(_a): `uuu FB: flash boot_a boot-zx500.img`
- Windows(_b): `uuu FB: flash boot_b boot-zx500.img`

## 技术发现

### Fastboot模式进入方式

开机时同时按住音量减键与快进键

### NVP系统

所有配置参数、标志位及密钥均以原始字段形式存储于nvp分区。`/vendor/bin`目录下的`nvp`、`nvpflag`、`nvpinfo`、`nvpnode`、`nvpstr`及`nvptest`为nvp操作调试工具：
- `nvp`：十六进制显示nvp分区内容
- `nvpflag`：查看/写入区域标志等参数
- `nvpstr`：控制nvp中的字符串变量
- 其余工具功能尚未明确
