# UACME Method 43 (`ucmDccwCOMMethod`) — Flow

**Entry:** `methods.c:MethodDccwCOM` → `hybrids.c:ucmDccwCOMMethod`  ·  **Artifact summary:** two-broker COM chain — CMSTPLUA writes custom calibrator path to HKLM, ColorDataProxy launches dccw.exe which reads that path and runs the payload

| # | Behavior (`actor action artifact`) | Artifact [class] → consumed by | Tactic / TID — Technique Name | Context (baseline) |
|---|---|---|---|---|
| 1 | Akagi overwrites own PEB `ImagePathName` and `CommandLine` to impersonate `explorer.exe` | PEB fields [memory] → #2, #4 | — | PEB path differs from actual image on disk |
| 2 | Akagi calls `CoGetObject` with elevation moniker to instantiate CMSTPLUA COM object at High IL | elevated DllHost.exe surrogate [process] → #3 | — | `Elevation:Administrator!new:` moniker string in COM activation |
| 3 | DllHost (CMSTPLUA) calls `ICMLuaUtil::SetRegistryStringValue` to write payload path to `HKLM\...\Display Calibration\CalibratorApplications` | HKLM registry value [registry] → #5 | — | protected HKLM write performed via elevated COM, not by user directly |
| 4 | Akagi calls `CoGetObject` with elevation moniker to instantiate ColorDataProxy COM object at High IL | elevated DllHost.exe surrogate [process] → #5 | — | second COM elevation in same process |
| 5 | DllHost (ColorDataProxy) calls `IColorDataProxy::LaunchDccw` which spawns `dccw.exe`; dccw reads calibrator path from HKLM and executes the payload | payload process at High IL [process] | — | payload parent is dccw.exe (not DllHost), grandchild of the COM chain |
| 6 | DllHost (CMSTPLUA) calls `ICMLuaUtil::DeleteRegistryStringValue` to remove the calibrator key | HKLM value deleted [registry] | — | self-cleanup of hijack artifact |
