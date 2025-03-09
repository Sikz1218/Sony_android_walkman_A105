# 安卓Walkman NW-A100系列设备技术文档

## 操作指南

### <ins>免责声明</ins>
本指南仅适用于Windows设备，仅提供给已掌握基本终端命令、adb或fastboot的高级用户使用。需预先安装ADB工具。

若操作导致设备变砖/损坏，您将自行承担一切后果，本人概不负责且不接受任何投诉。本指南不提供终端用户技术支持，不教授adb安装或基础命令执行等入门知识。

### 前期步骤

请先完成以下操作：

1. 在Walkman开发者选项中启用USB调试
2. 将Walkman连接至Windows电脑
3. 验证adb可用性，执行`adb shell getprop ro.boot.slot_suffix`
4. 记录输出结果（应为"_a"或"_b"）
5. 前往[releases页面](https://github.com/Sikz1218/unlock-and-root_android_walkman_A100-series/releases/tag/v0.0.1)下载相关文件
6. 将所有文件放入工作目录，执行`uuu`验证工具可用性

命令输出结果将作为执行`uuu`命令时的分区后缀参考

### Bootloader解锁

此操作将清除所有用户数据
1. 在开发者选项中启用OEM解锁和ADB调试
2. 执行`adb reboot bootloader`进入fastboot模式
3. 在fastboot模式（SONY logo界面）执行：`uuu FB: oem unlock`
4. 执行后设备会进入假死状态，实际正在进行用户分区擦除（约需500秒）
5. 完成后执行重启命令：`uuu FB: reboot`

### 禁用AVB

使用自定义内核需执行此步骤。通过以下命令刷写空白vbmeta文件禁用AVB：
- (若当前槽位为_a): `uuu FB: flash vbmeta_a blank_vbmeta.img`
- (若当前槽位为_b): `uuu FB: flash vbmeta_b blank_vbmeta.img`

首次启动将进入bootloop，随后进入恢复模式提示系统启动失败。此时需选择恢复出厂设置选项（使用音量键导航，电源键确认）。重置后系统应正常启动

### 内核

安装apatch.apk并使用apatch修补boot.img，将修补好的boot文件移动至工作目录，进入fastboot后执行：
- (若当前槽位为_a): `uuu FB: flash boot_a 你修补的boot文件名.img`
- (若当前槽位为_b): `uuu FB: flash boot_b 你修补的boot文件名.img`

## 技术发现

### Fastboot模式进入方式

开机时同时按住音量减键与快进键
若需进入恢复菜单，请连续按压音量加键与电源键2～5次

### NVP系统

所有配置参数、标志位及密钥均以原始字段形式存储于nvp分区。`/vendor/bin`目录下的`nvp`、`nvpflag`、`nvpinfo`、`nvpnode`、`nvpstr`及`nvptest`为nvp操作调试工具：
- `nvp`：十六进制显示nvp分区内容
- `nvpflag`：查看/写入区域标志等参数
- `nvpstr`：控制nvp中的字符串变量
- 其余工具功能尚未明确
