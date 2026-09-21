# iQOO Neo8 GhostLock 适配 (CVE-2026-43499)

**机型**: iQOO Neo8 PD2301 · 骁龙8+ Gen1 · kernel `5.10.246-gki-gb142569a7856`
**固件**: OriginOS `PD2301_A_16.3.16.1.W10.V000L1`
**漏洞**: CVE-2026-43499 (GhostLock 谱系, futex PI UAF)

## 目录
| 路径 | 说明 |
|---|---|
| `targets/` | Neo8 专属 48 宏 target.h ×2 + offsets.json |
| `logs/` | 15 轮全实测 log(345 行, R8 版) |
| `patches/` | R7/R9 + fd_set 补丁 diff + 7 条兼容情报 |
| `.github/workflows/` | NDK 一键编译 pipeline |

## 实测状态 (2026-09-22 晨)
| 阶段 | 状态 |
|---|---|
| kernelsnitch 碰撞审计 | ✅ 全命中 |
| kernel page prepare | ✅ attempt=1 |
| pselect route setup | ✅ LOCK=0x0E80 / W0=0x1180 / TASK=0x1280 |
| requeue fallback | ✅ R7 fix 15/15 不死锁 |
| **PI write primitive** | ❌ calls=0 success=0 — 5.10 fd_set 无 kernel write |
| root | 未取得 |

## 结论
本地已尽; 待 **kinser R11 `set_pselect_write`** 或 **月虹 Neo8 R12 完整包** 接手最后一层。

## 引用 / 参考（此仓库不包含其代码，仅作出处声明）
本仓库的源码与思路全部来自以下上游仓库，**本仓库只收 Neo8 专属适配产物**（target.h / offsets.json / 实测 log / 补丁 diff），以示尊重并避免重复搬运。需要完整源码请前往原仓库：

| 上游仓库 | 在本项目里的角色 |
|---|---|
| · `joinchchang/ghostlock-oneplus`（JoinChang） · **GhostLock host**（rooted 主要 payload host） | ghostlock 源码逻辑主体 + `run_main_route_threads` / pselect route 的全链路执行 |
| · `NebuSec/CyberMeowfia` | CVE-2026-43499 漏洞细节原始信息 + IonStack 版本的提权无尽展開 |
| · `x-spy/CVE-2026-43499-popsicle` | 另一个 CVE-2026-43499 参考实现（drivers/cheese 路线）|
| · `YuKongA/payload_extract_rs` | OTA payload 解析管线（流式 Range 只拉 boot.img 的方案来源）|
| · `vmlinux-to-elf`（martinosevilla）| 从 Neo8 stock boot.img 解出 kallsyms 符号表 176,599 条，供 target.h 48 宏取值 |
| · `gitee.com/ytngtaoaaa/allroot` | 社区适配流程三阶段框架（boot 采集 → AI 编译 → log 迭代）|
| · `kinser "CVE-2026-43499 在 iQOO Neo8 上的适配分析报告"` | R9 MM_STRUCT_SZ=0x3C0 / R10 PAGE_MM_STRUCT_SZ / R11`set_pselect_write` 情报源 — **write layer 缺位的最终答案在这里面** |
| · **月虹提权助手**（каждо世代 .so 包提供了集→ 本项目为它产 Neo8 slot 的第三方适配） | 周 45 个 .so 包规范参考 / com.ghostlock.app 内置 65768B 版本（实测唯一走到 spawn root_child 的版本）|

若你是上述仓库的作者，看到这引用不满意或要求下线，请开 Issue，我会移除对应产物。

