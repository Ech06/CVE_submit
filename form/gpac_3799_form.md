# GPAC Issue #3799 漏洞提交表单

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
GPAC MP4Box XMT场景脚本加载功能
```

## 漏洞类型（下拉框）

```
拒绝服务
```

## PoC

[poc_3799](https://github.com/Ech06/CVE_submit/blob/main/pocs/poc_3799)

## 触发过程

```
git checkout f1219cde
./configure --enable-sanitizer --static-mp4box
make -j$(nproc)
./bin/gcc/MP4Box -add ../pocs/poc_3799 -new /tmp/out.mp4

预期报错：
ERROR: AddressSanitizer: heap-use-after-free
READ of size 8
SUMMARY: AddressSanitizer: heap-use-after-free in gf_sg_script_load
```

## 漏洞URL

```
https://github.com/gpac/gpac,https://github.com/gpac/gpac/issues/3799,https://github.com/gpac/gpac/commit/f1219cde1a892a913ec85d7fd97324ece94c2d15,https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 漏洞描述

```
GPAC 是一款开源多媒体处理框架，MP4Box 是其媒体封装、导入和场景处理工具。GPAC f1219cde 版本的 XMT 场景脚本加载功能存在释放后重用漏洞。攻击者可构造包含异常节点关系和错误结束标签的 XMT 文件，使解析器释放已丢弃的 Script 节点后仍将其加入 scripts_to_load 列表。场景命令应用阶段的 gf_sg_script_load 会读取该悬空指针，造成堆释放后重用和进程崩溃。该漏洞可用于拒绝服务，并可能影响所有自动处理不可信 XMT 场景的应用或服务。
```

## 临时解决方案

```
在无法立即升级时，禁止导入来源不可信的 XMT/X3D 场景文件；在业务入口使用严格的 XML 结构校验并拒绝节点嵌套或结束标签不匹配的文件；将 MP4Box 置于低权限隔离环境中运行，并限制任务执行时间和资源用量。
```

## 正式解决方案

```
厂商已在提交 9eb40df4448b88d6a6ce3454657c06f47eff0b24 中修复该漏洞。补丁记录节点是否已被丢弃，并阻止已释放节点进入延迟脚本加载列表。建议升级到包含该提交的最新 GPAC 版本。补丁链接：https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 复现截图

```
pictures/1785762022701.png
```
