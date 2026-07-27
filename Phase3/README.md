# Phase 3 — 收集对象并导出

本目录从官方 `UnrealEngine-release/Engine` 拷贝阶段 3 相关模块。

| 文档 | 用途 |
|------|------|
| [READ_LIST.md](./READ_LIST.md) | 文件清单 + 优先级勾选 |
| [READING_GUIDE.md](./READING_GUIDE.md) | 收集→导出调用链、Swarm 契约、阅读顺序 |
| [FILE_INDEX.txt](./FILE_INDEX.txt) | 本目录全部文件路径索引 |

```
阶段 2 参数 + Mapping 抽象已就绪
  → FStaticLightingSystem::GatherStaticLightingInfo（Primitive → Mesh/Mapping）
  → GatherScene（WorldSettings / Volume / Portal → Exporter）
  → FLightmassExporter 经 Swarm 写出 scene / mesh / material / mapping
  → KickoffSwarm → UnrealLightmass（或 GPULightmass 旁路）求解（阶段 4）
```

> 注：同文件中的 `CompleteRun` / `ApplyNewLightingData` / `EncodeTextures` 属于**写回**阶段 5。
