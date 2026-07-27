# Phase 1 — 导入网格 → 展开 / 指定 Lightmap UV

本目录从 `Core/Engine` 再摘录**阶段 1**相关模块。

| 文档 | 用途 |
|------|------|
| [READ_LIST.md](./READ_LIST.md) | 文件清单 + 优先级勾选 |
| [READING_GUIDE.md](./READING_GUIDE.md) | 调用链、关键符号、阅读顺序与笔记要点 |
| [FILE_INDEX.txt](./FILE_INDEX.txt) | 本目录全部文件路径索引 |

```
导入 (FBX / Interchange)
  → 写入 FMeshBuildSettings / LightMapCoordinateIndex
  → StaticMesh 构建 (MeshBuilder)
  → CreateLightMapUVLayout / FLayoutUV
  → 目标 UV 通道可供后续烘培使用
```
