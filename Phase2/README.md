# Phase 2 — 设置烘培与场景参数

本目录从官方 `UnrealEngine-release/Engine`。

| 文档 | 用途 |
|------|------|
| [READ_LIST.md](./READ_LIST.md) | 文件清单 + 优先级勾选 |
| [READING_GUIDE.md](./READING_GUIDE.md) | 参数层级、关键符号、阅读顺序 |
| [FILE_INDEX.txt](./FILE_INDEX.txt) | 本目录全部文件路径索引 |

```
阶段 1 已就绪的 Lightmap UV / LightMapCoordinateIndex
  → 世界级 FLightmassWorldInfoSettings（质量、反弹、AO…）
  → 灯光 / Primitive / Volume 覆盖
  → 每物体 Lightmap 分辨率（资产 or Component Override）
  → FStaticLightingTextureMapping（Size + UV Index）供阶段 3 收集导出
```
