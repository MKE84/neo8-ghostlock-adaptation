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
