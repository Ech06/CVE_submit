# GPAC Issue #3800 漏洞提交表单

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
GPAC MP4Box 场景图销毁功能
```

## 漏洞类型（下拉框）

```
拒绝服务
```

## PoC

[poc_3800](https://github.com/Ech06/CVE_submit/blob/main/pocs/poc_3800)

## 触发过程

```
git checkout f1219cde
./configure --enable-sanitizer --static-mp4box
make -j$(nproc)
./bin/gcc/MP4Box -add ../pocs/poc_3800 -new /tmp/out.mp4

预期报错：
ERROR: AddressSanitizer: heap-use-after-free
READ of size 8
SUMMARY: AddressSanitizer: heap-use-after-free in gf_node_changed_internal
```

## 漏洞URL

```
https://github.com/gpac/gpac,https://github.com/gpac/gpac/issues/3800,https://github.com/gpac/gpac/commit/f1219cde1a892a913ec85d7fd97324ece94c2d15,https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 漏洞描述

```
GPAC 是一款开源多媒体处理框架，MP4Box 是其媒体封装、导入和场景处理工具。GPAC f1219cde 版本的场景图销毁流程存在释放后重用漏洞。攻击者可提交恶意场景文件构造不一致的节点及原型关系。场景清理过程中，gf_node_replace 在通知父节点之前注销并释放旧节点，随后 gf_node_changed_internal 继续访问已释放的场景图内存，导致堆释放后重用和进程崩溃。成功利用可造成处理任务或服务拒绝服务。
```

## 临时解决方案

```
在无法立即升级时，不要使用 MP4Box 处理来源不明的 BT、XMT 或其他场景描述文件；对输入实施格式白名单和结构校验；将文件转换任务放入独立低权限进程或容器，设置超时、内存限制和异常退出重试策略。
```

## 正式解决方案

```
厂商已在提交 9eb40df4448b88d6a6ce3454657c06f47eff0b24 中修复该漏洞，将父节点变更通知调整到旧节点注销和释放之前执行。建议升级到包含该提交的最新 GPAC 版本。补丁链接：https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 复现截图

```
pictures/1785762107168.png
```
