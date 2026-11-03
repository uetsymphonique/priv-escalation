# UACME Method 39 (`ucmCorProfilerMethod`) — Flow

**Entry:** `methods.c:MethodCorProfiler` → `hybrids.c:ucmCorProfilerMethod`  ·  **Artifact summary:** sets COR_PROFILER env vars pointing to proxy DLL in `%temp%`, then launches auto-elevating .NET application (`mmc.exe eventvwr.msc`) — CLR loads the profiler DLL at High IL

| # | Behavior (`actor action artifact`) | Artifact [class] → consumed by | Tactic / TID — Technique Name | Context (baseline) |
|---|---|---|---|---|
| 1 | Akagi generates a random GUID for profiler CLSID | — [no-artifact] → #3 | — | |
| 2 | Akagi writes Fubuki proxy DLL to `%temp%\<name>.dll` | temp DLL file [file] → #5 | — | DLL in user-writable temp directory |
| 3 | Akagi sets user env vars `COR_ENABLE_PROFILING=1`, `COR_PROFILER={GUID}`, `COR_PROFILER_PATH=%temp%\<name>.dll` | env vars [registry] → #5 | — | user-level env var manipulation; COR_PROFILER_PATH points to user-writable path |
| 4 | Akagi launches `mmc.exe eventvwr.msc` (auto-elevate, .NET application) | mmc.exe at High IL [process] → #5 | — | eventvwr snap-in is written in .NET, CLR initializes on load |
| 5 | CLR runtime inside elevated mmc.exe reads COR_PROFILER env vars and loads the profiler DLL at High IL | Fubuki DLL loaded at High IL [process] | — | profiler DLL loaded by CLR from user-writable temp path into elevated process |
| 6 | Akagi deletes env vars `COR_ENABLE_PROFILING`, `COR_PROFILER`, `COR_PROFILER_PATH` | env vars removed [registry] | — | self-cleanup of hijack artifacts |
