# UACME Method 23 (`ucmDismMethod`) — Flow

**Entry:** `methods.c:MethodDism` → `hybrids.c:ucmDismMethod`  ·  **Artifact summary:** elevated IFileOperation moves proxy DLL (Fubuki as `dismcore.dll`) into `system32`, then `pkgmgr.exe` auto-elevates and triggers `dism.exe` which loads the planted DLL at High IL

| # | Behavior (`actor action artifact`) | Artifact [class] → consumed by | Tactic / TID — Technique Name | Context (baseline) |
|---|---|---|---|---|
| 1 | Akagi overwrites own PEB `ImagePathName` and `CommandLine` to impersonate `explorer.exe` | PEB fields [memory] → #3 | — | PEB path differs from actual image on disk |
| 2 | Akagi writes Fubuki proxy DLL to `%temp%` as `dismcore.dll` (with trash-char suffix) | temp file [file] → #3 | — | DLL written to user-writable temp directory |
| 3 | Akagi calls `CoGetObject` with elevation moniker to instantiate IFileOperation at High IL; moves proxy DLL from `%temp%` to `C:\Windows\System32\` | `system32\dismcore.dll` [file] → #5 | — | IFileOperation auto-approved when caller PEB impersonates explorer.exe |
| 4 | Akagi renames proxy DLL in `system32` to remove trash-char suffix | renamed `dismcore.dll` [file] → #5 | — | elevated IFileOperation rename in protected directory |
| 5 | Akagi launches `pkgmgr.exe /ip /m:...` (auto-elevate binary); pkgmgr spawns `dism.exe` which loads `dismcore.dll` from `system32` (DLL search order hijack) | Fubuki DLL loaded at High IL [process] | — | dism.exe search-order loads dismcore.dll from system32 before dism subdirectory |
