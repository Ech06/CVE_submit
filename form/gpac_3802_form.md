# GPAC Issue #3802 漏洞提交表单

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
GPAC MP4Box BT/BIFS子节点解析功能
```

## 漏洞类型（下拉框）

```
拒绝服务
```

## PoC

```
git clone https://github.com/gpac/gpac.git gpac
cd gpac
git checkout f1219cde
./configure --enable-sanitizer --static-mp4box
make -j$(nproc)
./bin/gcc/MP4Box -add ../pocs/poc_3802 -new /tmp/out.mp4

执行后 AddressSanitizer 报告 SEGV，调用栈顶部为：
gf_node_list_get_child (scenegraph/base_scenegraph.c:1491)
gf_bt_parse_bifs_command (scene_manager/loader_bt.c:2358)
```

## 触发过程

```
1. 获取 GPAC 源代码并切换至存在漏洞的 f1219cde 版本。
2. 使用 --enable-sanitizer --static-mp4box 参数配置并编译 MP4Box。
3. 将畸形 BT/BIFS 场景文件 poc_3802 放置在 gpac 同级的 pocs 目录中。
4. 执行 ./bin/gcc/MP4Box -add ../pocs/poc_3802 -new /tmp/out.mp4。
5. 解析器处理畸形的索引 BIFS 命令时，未确认目标字段是否为 children 字段，便将目标字段内容作为子节点链表传给 gf_node_list_get_child。函数读取接近空地址的无效指针 0x8，触发 AddressSanitizer SEGV 并终止进程。
```

## 漏洞URL

```
https://github.com/gpac/gpac,https://github.com/gpac/gpac/issues/3802,https://github.com/gpac/gpac/commit/f1219cde1a892a913ec85d7fd97324ece94c2d15,https://github.com/gpac/gpac/commit/afca1f1181668d85941d51ed1adf647807d5d975
```

## 漏洞描述

```
GPAC 是一款开源多媒体处理框架，MP4Box 是其媒体封装、导入和场景处理工具。GPAC f1219cde 版本的 BT/BIFS 子节点解析功能存在空指针解引用漏洞。攻击者可构造恶意索引 BIFS 命令，使 gf_bt_parse_bifs_command 在未验证目标字段类型的情况下将字段内容解释为子节点链表。gf_node_list_get_child 随后读取接近空地址的无效指针并导致进程崩溃。该漏洞可使处理恶意场景文件的 MP4Box 或上层服务发生拒绝服务。
```

## 临时解决方案

```
在无法立即升级时，禁止 MP4Box 导入来源不可信的 BT/BIFS 文件；在进入 GPAC 前验证 BIFS 命令字段及索引合法性；使用低权限隔离进程处理媒体文件，并设置任务超时、资源限制和崩溃恢复机制。
```

## 正式解决方案

```
厂商已在提交 afca1f1181668d85941d51ed1adf647807d5d975 中修复该漏洞。补丁仅允许对 children 字段执行子节点链表查询，并在未找到有效节点时返回解析错误。建议升级到包含该提交的最新 GPAC 版本。补丁链接：https://github.com/gpac/gpac/commit/afca1f1181668d85941d51ed1adf647807d5d975
```

## 复现截图

```
pictures/1785762169085.png
```
