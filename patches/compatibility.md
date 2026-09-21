# Neo8 compatibility notes (PD2301 / 5.10.246)

1. `KIMAGE_TEXT_BASE = 0xffffffc008000000`, `P0_PHYS_OFFSET = P0_KERNEL_PHYS_LOAD = 0x80000000`, delta=0
2. Cornerstone struct fixes (proven by explicit kernelsnitch hit):
   - `MM_STRUCT_SZ 0x3C0` (kinser R9 修正, 0x500 不 work 12/12 derive 失败)
   - `STRUCT_PAGE_SIZE 0x48`, `STRUCT_SLAB_CACHE_OFF 0x18`
   - `STRUCT_PAGE_TYPE_OFF 0x30`
3. pselect_fd_set layout: `LOCK_OFF=0x0E80 / W0_OFF=0x1180 / FOPS_OFF=0x0F80 / FAKE_TASK_OFF=0x1280`
4. `SLIDE_RANDOM_BOOT_ID_DATA_OFF = 0x02c88500` = kernel physically loading的 ctl_table.data slot (8-aligned pointer to boot_id)
5. R7 fallback: `FUTEX_CMP_REQUEUE_PI` -> EDEADLK all times; switch to `FUTEX_WAKE_PRIVATE` — 实测: 15/15 rounds no deadlock
6. __最后 gap__:  5.10 原生 fd_set 由内核 buffer copy, cannot directly write kernel. This is what Kinser's private R11 `set_pselect_write` does (非公开).
7. GhostLock App com.ghostlock.app 内置的 65768B 内置版本 (succemliments 2026-09-22 01:06) 成功 spawn 了 root_child (tombstone_29/30/31 evidences) 但被 ReSukiSU feature check 的 `sched_setscheduler` (syscall 142) seccomp blocked. R11+ 修此层.

Fingerprint: PD2301_A_16.3.16.1.W10.V000L1
APK: com.ghostlock.app v1.x · 月虹助手 roro.stellar.yuehong v1.5.3 (via custom local payload `/data/local/tmp/preload.so`)
