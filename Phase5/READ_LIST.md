# Phase 5 Read List

优先级：P0 必读 → P1 导入与 Apply → P2 编码与存储资源 → P3 体积 / WP 可选。

路径均相对 `Core/Phase5/`。

---

## P0 — 写回流水线骨架

- [x] `Engine/Source/Editor/UnrealEd/Private/StaticLightingSystem/StaticLightingPrivate.h`  
  ```
  `FinishLightmassProcess`、`EncodeTextures`、`ApplyNewLightingData`、`ApplyMapping`、`CompleteDeterministicMappings`
  ```
- [x] `Engine/Source/Editor/UnrealEd/Private/Lightmass/Lightmass.h`  
  ```
  `CompleteRun`、`ImportMappings` / `ImportMapping`、`ProcessMapping`、
  `ImportTextureMapping`、`ImportLightMapData2DData`；`FTextureMappingImportHelper`
  ```
- [x] `Engine/Source/Runtime/Engine/Classes/Engine/MapBuildDataRegistry.h`  
  ```
  `FMeshMapBuildData`（LightMap / ShadowMap / IrrelevantLights）；
  `UMapBuildDataRegistry::AllocateMeshBuildData` / `GetMeshBuildData`
  ```
- [x] `Engine/Source/Runtime/Engine/Public/LightMap.h`  
  ```
  `FQuantizedLightmapData`、`FLightMap2D::AllocateLightMap`、`EncodeTextures`
  ```

---



## P1 — Swarm 导入 → Apply → Registry



### Processor 导入与应用

- [x] `Engine/Source/Editor/UnrealEd/Private/Lightmass/Lightmass.cpp`  
  ```
  按符号跳读（勿通读）：
  - `CompleteRun`（约 3543）→ `ImportMappings` / `ProcessAvailableMappings`
  - `ImportMappings`（约 3614）、`ImportMapping`（约 4397）
  - `ProcessMapping`（约 4436）→ `System.ApplyMapping`
  - `ImportTextureMapping`（约 4763）、`ImportLightMapData2DData`（约 4671）
  ```
- [x] `Engine/Source/Editor/UnrealEd/Private/StaticLightingSystem/StaticLightingSystem.cpp`  
  ```
  - `FinishLightmassProcess`（约 2448）：Invalidate → CompleteRun → Encode → ApplyNewLightingData
  - `ApplyMapping`（约 2697）：转发到 `TextureMapping->Apply`
  - `EncodeTextures`（约 1448）
  - `ApplyNewLightingData`（约 1467）
  - `CompleteDeterministicMappings`（约 1769）
  ```



### StaticMesh 写回落点

- [x] `Engine/Source/Runtime/Engine/Public/StaticMeshLight.h`
- [x] `Engine/Source/Runtime/Engine/Private/StaticMeshLight.cpp`  
  ```
  **重点**：`FStaticMeshStaticLightingTextureMapping::Apply`（约 240）：
  Registry → `AllocateMeshBuildData` → `FLightMap2D::AllocateLightMap` / `FShadowMap2D::AllocateShadowMap`
  ```
- [x] `Engine/Source/Runtime/Engine/Public/StaticLightingBuildContext.h`
- [x] `Engine/Source/Runtime/Engine/Private/StaticLightingBuildContext.cpp`  
  ```
  `GetOrCreateRegistryForActor` / Level：决定写进哪个 `UMapBuildDataRegistry`
  ```
- [x] `Engine/Source/Runtime/Engine/Public/StaticLighting.h`  
  ```
  Mapping 基类上的 `Apply` 虚接口（对照）
  ```
- [ ] `Engine/Source/Runtime/Engine/Private/MapBuildData.cpp`  
  ```
  Registry 分配 / 序列化 / 查找实现
  ```



### 量化数据契约（对照阶段 4）

- [x] `Engine/Source/Programs/UnrealLightmass/Public/ImportExport.h`  
  ```
  `FLightMapData2DData`、量化样本布局；与 `ImportTextureMapping` 读字段一一对应
  ```

---



## P2 — Encode、贴图资源、运行时参数

- [ ] `Engine/Source/Runtime/Engine/Private/LightMap.cpp`  
  ```
  **重点**：`AllocateLightMap`（约 2091）——挂起 pending allocation；
  `EncodeTextures`（约 2462）——拼 atlas、生成 `ULightMapTexture2D` / VT；
  `FQuantizedLightmapData` 序列化（约 3391）
  ```
- [ ] `Engine/Source/Runtime/Engine/Public/ShadowMap.h`
- [ ] `Engine/Source/Runtime/Engine/Private/ShadowMap.cpp`  
  ```
  `AllocateShadowMap`、`EncodeTextures`（与 LightMap 对称）
  ```
- [ ] `Engine/Source/Runtime/Engine/Classes/Engine/LightMapTexture2D.h`
- [ ] `Engine/Source/Runtime/Engine/Classes/Engine/ShadowMapTexture2D.h`  
  ```
  编码产物 UObject 类型
  ```
- [ ] `Engine/Source/Runtime/Engine/Public/LightmapUniformShaderParameters.h`
- [ ] `Engine/Source/Runtime/Engine/Private/LightmapUniformShaderParameters.cpp`  
  ```
  `LightMapCoordinateScaleBias` 等：atlas UV 变换（阶段 6 采样用）
  ```
- [ ] `Engine/Source/Runtime/Engine/Classes/VT/LightmapVirtualTexture.h`
- [ ] `Engine/Source/Runtime/Engine/Private/VT/LightmapVirtualTexture.cpp`  
  ```
  VT Lightmap 路径；与 `Apply` 中 `UseVirtualTextureLightmap` 分支对照
  ```

---



## P3 — 体积光 / World Partition 存储（可选）

- [ ] `Engine/Source/Editor/UnrealEd/Private/Lightmass/ImportVolumetricLightmap.cpp`  
  ```
  `CompleteRun` 里 `ImportVolumetricLightmap` 旁路
  ```
- [ ] `Engine/Source/Runtime/Engine/Public/PrecomputedVolumetricLightmap.h`
- [ ] `Engine/Source/Runtime/Engine/Private/PrecomputedVolumetricLightmap.cpp`
- [ ] `Engine/Source/Runtime/Engine/Public/PrecomputedVolumetricLightmapStreaming.h`
- [ ] `Engine/Source/Runtime/Engine/Private/PrecomputedVolumetricLightmapStreaming.cpp`
- [ ] `Engine/Source/Runtime/Engine/Public/WorldPartition/StaticLightingData/MapBuildDataActor.h`
- [ ] `Engine/Source/Runtime/Engine/Private/WorldPartition/StaticLightingData/MapBuildDataActor.cpp`
- [ ] `Engine/Source/Runtime/Engine/Public/WorldPartition/StaticLightingData/StaticLightingDescriptors.h`  
  ```
  WP 下 MapBuildData 的 Actor/Descriptor 存放方式
  ```

---



## 模块汇总（本 Phase 已收录）


| 区域                   | 目录                                              | 文件数（约） |
| -------------------- | ----------------------------------------------- | ------ |
| Editor 导入/收尾         | `UnrealEd/.../Lightmass`、`StaticLightingSystem` | 4      |
| Mapping Apply        | `StaticMeshLight.*`、`StaticLighting*`           | 5      |
| LightMap / ShadowMap | `LightMap.*`、`ShadowMap.*`、Texture2D 头          | 6      |
| Registry             | `MapBuildDataRegistry.h`、`MapBuildData.cpp`     | 2      |
| Uniform / VT         | `LightmapUniform*`、`LightmapVirtualTexture.*`   | 4      |
| 契约 / 体积 / WP         | `ImportExport.h` + P3 文件                        | 9      |


完整路径见 [FILE_INDEX.txt](./FILE_INDEX.txt)。