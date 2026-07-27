# Phase 5 — 写回构建数据

本目录从官方 `UnrealEngine-release/Engine` 拷贝阶段 5 相关模块。

| 文档 | 用途 |
|------|------|
| [READ_LIST.md](./READ_LIST.md) | 文件清单 + 优先级勾选 |
| [READING_GUIDE.md](./READING_GUIDE.md) | 导入→Apply→Encode→Registry 调用链与阅读顺序 |
| [FILE_INDEX.txt](./FILE_INDEX.txt) | 本目录全部文件路径索引 |

```
阶段 4：量化 Lightmap / Shadow 已就绪
  （CPU：Swarm 通道可 Read；GPU：常已在 Plugin 内 Encode）
  → FLightmassProcessor::ImportTextureMapping / ImportLightMapData2DData
  → ProcessMapping → TextureMapping::Apply
  → FLightMap2D::AllocateLightMap → FMeshMapBuildData（Registry）
  → EncodeTextures（拼 atlas / 生成 UTexture）
  → ApplyNewLightingData
  → 阶段 6：运行时按 UV 采样
```

> GPULightmass 可能在阶段 4 末尾直接调用 `FLightMap2D::EncodeTextures`；本 Phase 仍以 **Editor 经典写回路径**为主干，并标出与 GPU 的汇合点。
