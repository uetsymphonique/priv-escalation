# UACME Method 41 (`ucmCMLuaUtilShellExecMethod`) — Flow

**Entry:** `methods.c:MethodCMLuaUtil` → `api0cradle.c:ucmCMLuaUtilShellExecMethod`  ·  **Artifact summary:** COM elevation moniker instantiates CMSTPLUA at High IL, calls `ShellExec` to launch payload as elevated child of DllHost.exe

| # | Behavior (`actor action artifact`) | Artifact [class] → consumed by | Tactic / TID — Technique Name | Context (baseline) |
|---|---|---|---|---|
| 1 | Akagi overwrites own PEB `ImagePathName` and `CommandLine` to impersonate `explorer.exe` | PEB fields [memory] → #2 | — | PEB path differs from actual image on disk |
| 2 | Akagi calls `CoGetObject` with elevation moniker to instantiate CMSTPLUA COM object at High IL | elevated DllHost.exe surrogate [process] → #3 | — | `Elevation:Administrator!new:` moniker string in COM activation |
| 3 | DllHost (CMSTPLUA) calls `ICMLuaUtil::ShellExec` to launch payload executable | payload process at High IL [process] | — | child of DllHost.exe running at High integrity |
