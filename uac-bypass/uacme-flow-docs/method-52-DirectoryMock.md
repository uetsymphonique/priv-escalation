# UACME Method 52 (`ucmDirectoryMockMethod`) — Flow

**Entry:** `methods.c:MethodDirectoryMock` → `dwells.c:ucmDirectoryMockMethod`  ·  **Artifact summary:** creates mock trusted directory `C:\Windows ` (trailing space) via NT API, junctions it to `%temp%\system32` where Fubuki proxy DLL and winsat.exe are staged, then launches winsat from the mock path — AIS GetLongPathNameW resolves it as trusted and auto-elevates, loading the proxy DLL at High IL

| # | Behavior (`actor action artifact`) | Artifact [class] → consumed by | Tactic / TID — Technique Name | Context (baseline) |
|---|---|---|---|---|
| 1 | Akagi creates `%temp%\system32\` directory | temp directory [file] → #2, #3 | — | staging directory in user-writable temp |
| 2 | Akagi writes Fubuki proxy DLL to `%temp%\system32\winmm.dll` | proxy DLL file [file] → #6 | — | DLL name matches known winsat.exe import (DLL search order) |
| 3 | Akagi copies `C:\Windows\System32\winsat.exe` to `%temp%\system32\winsat.exe` | winsat copy [file] → #6 | — | legitimate auto-elevate binary copied to user-controlled directory |
| 4 | Akagi calls `NtCreateFile` to create directory `\\??\C:\Windows ` (trailing space) | mock trusted directory [file] → #5 | — | NT API bypasses Win32 trailing-space stripping; directory mimics `C:\Windows` |
| 5 | Akagi sets NTFS reparse point (junction) on `C:\Windows ` → `%temp%` | junction reparse point [file] → #6 | — | `C:\Windows \system32\` resolves to `%temp%\system32\` |
| 6 | Akagi launches `C:\Windows \system32\winsat.exe`; AIS calls `GetLongPathNameW` which strips trailing space → resolves as `C:\Windows\System32\winsat.exe` (trusted) → auto-elevates; actual DLL load uses the mock path → loads `winmm.dll` (Fubuki) from `%temp%\system32\` | winsat.exe at High IL + Fubuki DLL loaded [process] | — | path trust check and DLL load use different resolution paths |
| 7 | Akagi removes junction reparse point from `C:\Windows ` | reparse point removed [file] → #8 | — | self-cleanup |
| 8 | Akagi calls `NtDeleteFile` to remove `C:\Windows ` directory | mock directory removed [file] | — | self-cleanup |
