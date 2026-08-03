# GPAC Issue #3801 漏洞提交表单

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
GPAC MP4Box XMT原型节点销毁功能
```

## 漏洞类型（下拉框）

```
拒绝服务
```

## PoC

[poc_3801](https://github.com/Ech06/CVE_submit/blob/main/pocs/poc_3801)

## 触发过程

```
git checkout f1219cde
./configure --enable-sanitizer --static-mp4box
make -j$(nproc)
./bin/gcc/MP4Box -add ../pocs/poc_3801 -new /tmp/out.mp4

预期报错：
ERROR: AddressSanitizer: heap-use-after-free
READ of size 8
SUMMARY: AddressSanitizer: heap-use-after-free in gf_node_unregister
```

## 漏洞URL

```
https://github.com/gpac/gpac,https://github.com/gpac/gpac/issues/3801,https://github.com/gpac/gpac/commit/f1219cde1a892a913ec85d7fd97324ece94c2d15,https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 漏洞描述

```
GPAC 是一款开源多媒体处理框架，MP4Box 是其媒体封装、导入和场景处理工具。GPAC f1219cde 版本的 XMT 原型节点销毁流程存在释放后重用漏洞。攻击者可构造包含非法嵌套 ProtoDeclare 和异常节点关系的 XMT 文件，使原型默认节点引用在原型子图释放后继续存在。后续 OrderedGroup 销毁过程会通过 gf_node_unregister 访问已释放的场景图内存，造成堆释放后重用并使 MP4Box 崩溃，进而形成拒绝服务。
```

## 临时解决方案

```
在无法立即升级时，禁止处理来源不可信的 XMT 文件；使用 XML/XMT 预解析器拒绝嵌套 ProtoDeclare、未闭合元素和节点关系异常的输入；将 MP4Box 运行在低权限隔离环境中，并限制单次任务的执行时间和资源。
```

## 正式解决方案

```
厂商已在提交 9eb40df4448b88d6a6ce3454657c06f47eff0b24 中修复该漏洞。补丁拒绝节点内部嵌套的 ProtoDeclare，并在原型子图销毁前正确注销 SFNode/MFNode 默认节点引用。建议升级到包含该提交的最新 GPAC 版本。补丁链接：https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 复现截图

```
pictures/1785762143155.png
```
