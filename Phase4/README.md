# Phase 4 — 求解光照（CPU / GPU 分轨）

本目录从官方 `UnrealEngine-release/Engine` 拷贝阶段 4 相关模块。

| 文档 | 用途 |
|------|------|
| [READ_LIST.md](./READ_LIST.md) | 文件清单（**4a CPU / 4b GPU** 分节）+ 优先级勾选 |
| [READING_GUIDE.md](./READING_GUIDE.md) | 两条求解路径对照、关键符号、阅读顺序 |
| [FILE_INDEX.txt](./FILE_INDEX.txt) | 本目录全部文件路径索引 |

```
阶段 3：Swarm 已写出 scene/mesh/material/mapping
        或 GPULightmass 已 RegisterImplementation
    ├─ 4a CPU UnrealLightmass
    │     ImportScene → Photon / Radiosity / Final Gather
    │     → ProcessTextureMapping（沿 LM UV 栅格化）→ 回传量化 LM
    └─ 4b GPULightmass
          场景 RT 结构 → Tile 路径追踪 → Encode → 同一套 LightMap 存储
→ 阶段 5：写回 MapBuildData / EncodeTextures
```

| 轨道 | 目录根 | 约文件数 |
|------|--------|----------|
| 共享钩子 | `Engine/Source/Runtime/Engine/.../StaticLightingSystemInterface.*` | 2 |
| **4a CPU** | `Engine/Source/Programs/UnrealLightmass/` | ~77 |
| **4b GPU** | `Engine/Plugins/Experimental/GPULightmass/` | ~54 |
