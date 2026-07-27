# Phase 1 Read List

优先级：P0 必读 → P1 主路径 → P2 导入侧 → P3 对照/可选。

---

## P0 — 数据契约

- [x] `Engine/Source/Runtime/Engine/Classes/Engine/EngineTypes.h`  
  ```
  定位 `FMeshBuildSettings`：`bGenerateLightmapUVs`、`SrcLightmapIndex`、`DstLightmapIndex`、`MinLightmapResolution`
  ```
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Public/MeshUtilitiesCommon.h`  
  ```
  `ELightmapUVVersion`
  ```
- [x] `Engine/Source/Runtime/Engine/Classes/Engine/StaticMesh.h`  
  ```
  `LightMapCoordinateIndex`、`LightMapResolution`、`LightmapUVVersion` / `GetLightmapUVVersion`
  ```

---

## P1 — UV 展开 / 打包核心

### MeshUtilitiesCommon

- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Public/LayoutUV.h`
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Private/LayoutUV.cpp`  
  ```
  `FindCharts` → `FindBestPacking` → `CommitPackedUVs`；内部 `FChartFinder` / `FChartPacker`
  ```
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Public/OverlappingCorners.h`
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Private/OverlappingCorners.cpp`
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Public/Allocator2D.h`
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Private/Allocator2D.cpp`
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/Public/DisjointSet.h`  
  ```
  Chart 连通/合并辅助（随 LayoutUV 一起看）
  ```
- [x] `Engine/Source/Runtime/MeshUtilitiesCommon/MeshUtilitiesCommon.Build.cs`  
  ```
  （可选）模块依赖一瞥
  ```



### MeshDescription 适配层

- [x] `Engine/Source/Runtime/StaticMeshDescription/Public/StaticMeshOperations.h`  
  ```
  `CreateLightMapUVLayout`、`GetUVChartCount` 声明
  ```
- [x] `Engine/Source/Runtime/StaticMeshDescription/Private/StaticMeshOperations.cpp`  
  ```
  **重点**：`CreateLightMapUVLayout`（约 1963 行起）、`FLayoutUVMeshDescriptionView`
  ```
- [x] `Engine/Source/Runtime/StaticMeshDescription/StaticMeshDescription.Build.cs`  
  ```
  （可选）
  ```



### 构建时调用链

- [x] `Engine/Source/Developer/MeshBuilder/Public/MeshDescriptionHelper.h`
- [x] `Engine/Source/Developer/MeshBuilder/Private/MeshDescriptionHelper.cpp`  
  ```
  **重点**：`bGenerateLightmapUVs` 分支 → `CreateLightMapUVLayout`（约 101–128 行）
  ```
- [x] `Engine/Source/Developer/MeshBuilder/Public/StaticMeshBuilder.h`
- [x] `Engine/Source/Developer/MeshBuilder/Private/StaticMeshBuilder.cpp`  
  ```
  `FMeshDescriptionHelper::SetupRenderMeshDescription` 调用点
  ```
- [x] `Engine/Source/Developer/MeshBuilder/MeshBuilder.Build.cs`  
  ```
  （可选）
  ```

---



## P2 — 导入侧如何写入设置



### FBX（经典导入）

- [ ] `Engine/Source/Editor/UnrealEd/Classes/Factories/FbxStaticMeshImportData.h`  
  ```
  `bGenerateLightmapUVs`
  ```
- [ ] `Engine/Source/Editor/UnrealEd/Private/Fbx/FbxStaticMeshImportData.cpp`  
  ```
  默认 `bGenerateLightmapUVs = true`
  ```
- [ ] `Engine/Source/Editor/UnrealEd/Private/Fbx/FbxStaticMeshImport.cpp`  
  ```
  `FindLightUVIndex` / `SetLightMapCoordinateIndex`；`BuildSettings.bGenerateLightmapUVs` 与 `DstLightmapIndex`（约 400、1867、2065 行一带）
  ```



### Interchange（现行导入管线）

- [ ] `Engine/Plugins/Interchange/Runtime/Source/Pipelines/Public/InterchangeGenericMeshPipeline.h`  
  ```
  Pipeline 上的 `bGenerateLightmapUVs`、`Src/DstLightmapIndex`、`MinLightmapResolution`
  ```
- [ ] `Engine/Plugins/Interchange/Runtime/Source/Pipelines/Private/InterchangeGenericStaticMeshPipeline.cpp`  
  ```
  `SetCustomGenerateLightmapUVs` 等写入 FactoryNode（约 471–478 行）
  ```
- [ ] `Engine/Plugins/Interchange/Runtime/Source/Pipelines/Private/InterchangeGenericMeshPipeline.cpp`  
  ```
  （对照）默认关闭/平台差异相关赋值
  ```
- [ ] `Engine/Plugins/Interchange/Runtime/Source/FactoryNodes/Public/InterchangeStaticMeshFactoryNode.h`
- [ ] `Engine/Plugins/Interchange/Runtime/Source/FactoryNodes/Private/InterchangeStaticMeshFactoryNode.cpp`  
  ```
  `ApplyCustom*ToAsset` → `FMeshBuildSettings` 字段
  ```

---



## P3 — 对照路径 / 大文件按需读

- [ ] `Engine/Source/Developer/MeshUtilities/Public/MeshUtilities.h`
- [ ] `Engine/Source/Developer/MeshUtilities/Private/MeshUtilities.cpp`  
  ```
  旧 **RawMesh** 路径：`FLayoutUVRawMeshView`（约 2505）、直接 `FLayoutUV`（约 2613）；与 MeshDescription 路径对照即可，不必通读全文件
  ```
- [ ] `Engine/Source/Developer/MeshUtilities/MeshUtilities.Build.cs`  
  ```
  （可选）
  ```
- [ ] `Engine/Source/Runtime/Engine/Private/StaticMesh.cpp`  
  ```
  大文件；按需搜 `LightMapCoordinateIndex` / `LightmapUVVersion` / Build 触发，确认资产侧如何持久化与重建
  ```

---



## 模块汇总（本 Phase 已收录）


| 模块 / 区域               | 目录                                             | 文件数（约） |
| --------------------- | ---------------------------------------------- | ------ |
| MeshUtilitiesCommon   | `Engine/Source/Runtime/MeshUtilitiesCommon/`   | 9      |
| StaticMeshDescription | `Engine/Source/Runtime/StaticMeshDescription/` | 3      |
| MeshBuilder           | `Engine/Source/Developer/MeshBuilder/`         | 5      |
| MeshUtilities         | `Engine/Source/Developer/MeshUtilities/`       | 3      |
| Engine（设置/资产）         | `Engine/Source/Runtime/Engine/...`             | 3      |
| FBX Import            | `Engine/Source/Editor/UnrealEd/.../Fbx*`       | 3      |
| Interchange           | `Engine/Plugins/Interchange/Runtime/...`       | 5      |


完整路径见 [FILE_INDEX.txt](./FILE_INDEX.txt)。