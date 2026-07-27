# Phase 3 Read List

优先级：P0 必读 → P1 收集 → P2 导出 / Swarm → P3 契约与旁路。

路径均相对 `Core/Phase3/`。

---

## P0 — 编排入口与心智模型

- [x] `Engine/Source/Editor/UnrealEd/Private/StaticLightingSystem/StaticLightingPrivate.h`  
  ```
  `FStaticLightingSystem` / `FStaticLightingManager` 声明：
  `BeginLightmassProcess`、`GatherStaticLightingInfo`、`GatherScene`、`KickoffSwarm`、`ApplyNewLightingData`
  ```
- [x] `Engine/Source/Runtime/Engine/Public/StaticLighting.h`  
  ```
  （承接 Phase2）`FStaticLightingMesh` / `FStaticLightingMapping` / `FStaticLightingTextureMapping`
  ```
- [x] `Engine/Source/Editor/UnrealEd/Private/Lightmass/Lightmass.h`  
  ```
  **重点**：`FLightmassExporter`（`SetLevelSettings` / `AddImportanceVolume` / `AddPortal` / `WriteToChannel`…）；
  `FLightmassProcessor`（`InitiateExport` / `BeginRun` / `Update` / `CompleteRun` / Swarm Job）
  ```

---



## P1 — 收集场景对象



### 主编排

- [x] `Engine/Source/Editor/UnrealEd/Private/StaticLightingSystem/StaticLightingSystem.cpp`  
  ```
  按符号跳读：
  - `CreateStaticLightingSystem`（约 299）
  - `BeginLightmassProcess`（约 537）→ 内部会走到收集
  - `GatherStaticLightingInfo`（约 1065）：`GetStaticLightingInfo` → `AddPrimitiveStaticLightingInfo`
  - `AddPrimitiveStaticLightingInfo`（约 2122）：填 `Meshes` / `Mappings`
  - `GatherScene`（约 2241）：WorldSettings、ImportanceVolume、Portal → Exporter
  - `KickoffSwarm`（约 2434）：`LightmassProcessor->BeginRun()`
  - （边界）`FinishLightmassProcess` / `ApplyNewLightingData`：阶段 5，略读标题即可
  ```



### Component → Mesh/Mapping

- [x] `Engine/Source/Runtime/Engine/Public/StaticMeshLight.h`
- [x] `Engine/Source/Runtime/Engine/Private/StaticMeshLight.cpp`  
  ```
  StaticMesh 如何用 `GetLightMapResolution` + `LightMapCoordinateIndex` 建 Texture Mapping；
  `ExportMeshInstance` / `ExportMapping` 的对端在 Export.cpp
  ```
- [x] `Engine/Source/Editor/UnrealEd/Private/StaticLightingSystem/StaticLightingExport.cpp`  
  ```
  **重点**：各类型 `ExportMeshInstance` / `ExportMapping` 如何 `AddUnique` 进 Exporter 列表，并 `AddLight` / `AddMaterial`
  ```
- [x] `Engine/Source/Runtime/Engine/Public/StaticLightingBuildContext.h`
- [x] `Engine/Source/Runtime/Engine/Private/StaticLightingBuildContext.cpp`  
  ```
  收集/写回时用的 Level 过滤、ImportanceBounds、MapBuildData Registry（与 Phase2 对照）
  ```

---



## P2 — 导出到 Swarm



### Exporter / Processor

- [x] `Engine/Source/Editor/UnrealEd/Private/Lightmass/Lightmass.cpp`  
  ```
  按符号跳读：
  - `FLightmassExporter::WriteToChannel`（约 682）→ `WriteStaticMeshes` / `WriteMappings` / `WriteLights`…
  - `WriteStaticMeshes`（约 1309）、`WriteMappings`（约 1959）
  - `FLightmassProcessor::OpenJob`（约 2827）、`InitiateExport`（约 2851）、`BeginRun`
  - `SwarmCallback`（约 399）：Task/Job 状态；本阶段理解「异步回传」即可，深度 Import 留阶段 5
  ```
- [x] `Engine/Source/Editor/UnrealEd/Private/Lightmass/LightmassRender.h`
- [x] `Engine/Source/Editor/UnrealEd/Private/Lightmass/LightmassRender.cpp`  
  ```
  材质属性如何栅格化成 Lightmass 可用的 material channel（`ExportMaterial` 路径）
  ```



### Swarm 通道 API

- [ ] `Engine/Source/Editor/SwarmInterface/Public/SwarmDefines.h`
- [ ] `Engine/Source/Editor/SwarmInterface/Public/SwarmInterface.h`  
  ```
  `OpenConnection`、`OpenChannel` / `WriteChannel` / `ReadChannel`、
  `BeginJobSpecification` / `AddTask`、Job/Task 消息类型
  ```
- [ ] `Engine/Source/Editor/SwarmInterface/Private/SwarmInterface.cpp`
- [ ] `Engine/Source/Editor/SwarmInterface/Private/SwarmInterfaceLocal.cpp`  
  ```
  远程 Agent vs 本地进程；跟一条 Write/OpenJob 即可
  ```
- [ ] `Engine/Source/Editor/SwarmInterface/Private/SwarmInterfaceModule.cpp`
- [ ] `Engine/Source/Editor/SwarmInterface/Private/SwarmMessages.h`
- [ ] `Engine/Source/Editor/SwarmInterface/SwarmInterface.Build.cs`  
  ```
  （可选）模块依赖
  ```

---



## P3 — 二进制契约 + 可插拔求解器 + 调试



### Editor ↔ UnrealLightmass 数据布局

- [ ] `Engine/Source/Programs/UnrealLightmass/Public/LightmassPublic.h`  
  ```
  伞头：汇总下列 Public 头
  ```
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/ImportExport.h`  
  ```
  Channel 扩展名 / Version；`FLightMapData2DData`、量化样本等（导出侧命名约定 + 导入侧结构预览）
  ```
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/SceneExport.h`  
  ```
  `FSceneFileHeader`、`FLightmassWorldInfoSettings` 对应的场景常量、灯光/Mesh/Mapping 序列化结构
  ```
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/MeshExport.h`  
  ```
  `FStaticMeshData` / LOD / Vertex（含 Lightmap UV）
  ```
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/MaterialExport.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/FluidExport.h`  
  ```
  （可选）材质 / Fluid 通道布局
  ```



### GPULightmass 旁路钩子

- [ ] `Engine/Source/Runtime/Engine/Public/Rendering/StaticLightingSystemInterface.h`
- [ ] `Engine/Source/Runtime/Engine/Private/Rendering/StaticLightingSystemInterface.cpp`  
  ```
  `IStaticLightingSystemImpl::RegisterImplementation`；CPU Lightmass 之外的注册点（阶段 4b）
  ```



### 调试（可选）

- [ ] `Engine/Source/Editor/UnrealEd/Private/StaticLightingSystem/StaticLightingTextureMapping.cpp`  
  ```
  `DebugThisMapping`：选中 texel 调试条件
  ```
- [ ] `Engine/Source/Editor/UnrealEd/Private/StaticLightingSystem/StaticLightingDebug.cpp`  
  ```
  静态光照调试辅助
  ```

---



## 模块汇总


| 模块 / 区域                | 目录                                                 | 文件数 |
| ---------------------- | -------------------------------------------------- | --- |
| StaticLightingSystem   | `.../UnrealEd/Private/StaticLightingSystem/`       | 5   |
| Lightmass 桥            | `.../UnrealEd/Private/Lightmass/`                  | 4   |
| Engine 抽象              | `StaticLighting*` / `StaticMeshLight*` / Interface | 6   |
| SwarmInterface         | `Engine/Source/Editor/SwarmInterface/`             | 7   |
| UnrealLightmass Public | `Engine/Source/Programs/UnrealLightmass/Public/`   | 6   |


完整路径见 [FILE_INDEX.txt](./FILE_INDEX.txt)。

**未收录：** `LightmassLandscapeRender.`*、`ImportVolumetricLightmap.cpp`、Swarm DotNET 工程（降噪；Landscape / 写回体积光 / 外部 Agent UI 非主干）。