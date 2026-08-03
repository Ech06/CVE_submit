# GPAC Issue #3798 漏洞提交表单

## 影响对象类型

```
应用程序
```

## 影响产品

```
gpac
```

## 影响产品版本

```
gpac f1219cde (master, 2026-07-25)
```

## 是否产品组件漏洞

```
是
```

## 漏洞名称（左侧输入框）

```
GPAC MP4Box BT/BIFS场景解析功能
```

## 漏洞类型（下拉框）

```
拒绝服务
```

## PoC

[poc_3798](https://github.com/Ech06/CVE_submit/blob/main/pocs/poc_3798)

## 触发过程

```
git checkout f1219cde
./configure --enable-sanitizer --static-mp4box
make -j$(nproc)
./bin/gcc/MP4Box -add ../pocs/poc_3798 -new /tmp/out.mp4

预期报错：
ERROR: AddressSanitizer: SEGV on unknown address 0x0000ffffffff
The signal is caused by a READ memory access.
SUMMARY: AddressSanitizer: SEGV in __sanitizer::internal_strlen(char const*)
```

## 漏洞URL

```
https://github.com/gpac/gpac,https://github.com/gpac/gpac/issues/3798,https://github.com/gpac/gpac/commit/f1219cde1a892a913ec85d7fd97324ece94c2d15,https://github.com/gpac/gpac/commit/afca1f1181668d85941d51ed1adf647807d5d975
```

## 漏洞描述

```
GPAC 是一款开源多媒体处理框架，MP4Box 是其媒体封装、导入和场景处理工具。GPAC f1219cde 版本的 BT/BIFS 场景解析功能存在拒绝服务漏洞。攻击者可构造恶意 BT/BIFS 场景文件，使 gf_bt_parse_bifs_command 进入未知命令错误处理分支。该分支调用 gf_bt_report 时使用了包含 %s 的格式字符串，却未传入对应字符串参数，导致 vsnprintf 将无效值作为字符串地址读取，并在 strlen 中触发非法内存访问。成功利用可使 MP4Box 进程崩溃，影响自动化媒体导入、文件转换以及处理不可信场景文件的服务。
```

## 临时解决方案

```
在无法立即升级时，不要使用 MP4Box 导入来源不可信的 BT/BIFS 场景文件；对上传文件实施来源校验、格式白名单和大小限制；将 MP4Box 放入低权限隔离进程或容器中运行，并为处理任务设置超时和崩溃重启策略，以降低进程异常退出造成的影响。
```

## 正式解决方案

```
厂商已在提交 afca1f1181668d85941d51ed1adf647807d5d975 中修复该漏洞，为 gf_bt_report 的 %s 格式化占位符补充正确的字符串参数。建议升级到包含该提交的最新 GPAC 版本。补丁链接：https://github.com/gpac/gpac/commit/afca1f1181668d85941d51ed1adf647807d5d975
```

## 复现截图

```
pictures/1785761895030.png
```
