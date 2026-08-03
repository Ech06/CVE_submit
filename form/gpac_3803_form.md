# GPAC Issue #3803 漏洞提交表单

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
GPAC MP4Box BT场景DEF节点解析功能
```

## 漏洞类型（下拉框）

```
拒绝服务
```

## PoC

```
https://raw.githubusercontent.com/Ech06/CVE_submit/main/pocs/poc_3803
```

## 触发过程

```
1. 获取 GPAC 源代码并切换至存在漏洞的 f1219cde 版本。
2. 使用 --enable-sanitizer --static-mp4box 参数配置并编译 MP4Box。
3. 将畸形 BT 场景文件 poc_3803 放置在 gpac 同级的 pocs 目录中。
4. 执行 ./bin/gcc/MP4Box -add ../pocs/poc_3803 -new /tmp/out.mp4。
5. gf_bt_sf_node 解析畸形节点失败后注销并释放半构造节点，但未从 parser->def_nodes 列表中删除相同指针。后续 DEF/USE 名称解析调用 gf_node_get_name 读取该悬空指针，触发 heap-use-after-free 并终止进程。
```

## 漏洞URL

```
https://github.com/gpac/gpac,https://github.com/gpac/gpac/issues/3803,https://github.com/gpac/gpac/commit/f1219cde1a892a913ec85d7fd97324ece94c2d15,https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 漏洞描述

```
GPAC 是一款开源多媒体处理框架，MP4Box 是其媒体封装、导入和场景处理工具。GPAC f1219cde 版本的 BT 场景 DEF 节点解析功能存在释放后重用漏洞。攻击者可构造包含异常 DEF/USE 或 PROTO 关系的 BT 场景，使节点解析错误路径释放半构造节点，却将其指针保留在 DEF 节点列表中。后续名称解析通过 gf_node_get_name 访问已释放内存，导致堆释放后重用和 MP4Box 进程崩溃，可形成拒绝服务。
```

## 临时解决方案

```
在无法立即升级时，不要处理来源不可信的 BT 场景文件；在导入前验证 DEF/USE 和 PROTO 节点关系，拒绝未定义引用及语法异常文件；使用低权限隔离进程运行 MP4Box，并设置超时、内存限制和异常退出恢复策略。
```

## 正式解决方案

```
厂商已在提交 9eb40df4448b88d6a6ce3454657c06f47eff0b24 中修复该漏洞，在释放解析失败的节点前先将其从 parser->def_nodes 列表删除。建议升级到包含该提交的最新 GPAC 版本。补丁链接：https://github.com/gpac/gpac/commit/9eb40df4448b88d6a6ce3454657c06f47eff0b24
```

## 复现截图

```
pictures/1785762202324.png
```
