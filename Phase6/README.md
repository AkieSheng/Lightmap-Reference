# Phase 6 — 运行时按 UV 采样并混合着色

本目录从官方 `UnrealEngine-release/Engine` 拷贝阶段 6 相关模块。

| 文档 | 用途 |
|------|------|
| [READ_LIST.md](./READ_LIST.md) | 文件清单 + 优先级勾选 |
| [READING_GUIDE.md](./READING_GUIDE.md) | VF → Policy → BasePass 采样混合链与阅读顺序 |
| [FILE_INDEX.txt](./FILE_INDEX.txt) | 本目录全部文件路径索引 |

```
阶段 5：FMeshMapBuildData + LightMap 贴图 + CoordinateScaleBias 已就绪
  → SceneProxy / FLightCacheInterface 绑定 LightMap
  → LocalVertexFactory 读 LM UV 通道 × scale/bias
  → BasePass 选 FUniformLightMapPolicy（HQ / LQ / VT / None…）
  → LightmapCommon.ush：GetLightMapColorHQ / LQ
  → 与直接光、其它间接光（ILC / VLM 等）在 BasePass 中混合
```
