# Phase 4 Read List

优先级：P0 必读 → P1 主求解路径 → P2 算法/加速细节 → P3 旁路与体积等可选。

**CPU（4a）与 GPU（4b）分节列出**。路径相对 `Core/Phase4/`。

---

# 共享 — 求解器选择钩子

- [x] `Engine/Source/Runtime/Engine/Public/Rendering/StaticLightingSystemInterface.h`
- [x] `Engine/Source/Runtime/Engine/Private/Rendering/StaticLightingSystemInterface.cpp`  
  ```
  `IStaticLightingSystem` / `IStaticLightingSystemImpl`；
  `RegisterImplementation`（GPULightmass 注册点）；与经典 Swarm+CPU 路径并列
  ```

---

# 4a — CPU UnrealLightmass

## P0 — 进程入口与场景导入

- [x] `Engine/Source/Programs/UnrealLightmass/Private/Launch/UnrealLightmass.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Launch/UnrealLightmass.cpp`  
  ```
  `main`（约 502）：连 Swarm、拉任务、进求解
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Private/CPUSolver/CPUSolver.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/CPUSolver/CPUSolver.cpp`  
  ```
  CPU 求解调度入口（与 Launch 衔接）
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Public/LightmassPublic.h`  
  ```
  伞头 → ImportExport / SceneExport / MeshExport / MaterialExport
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/LightmassSwarm.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/LightmassSwarm.cpp`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Importer.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Importer.cpp`  
  ```
  **重点**：`ImportScene`；Lights / StaticMeshInstances / TextureMappings / Materials
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/LightmassScene.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/LightmassScene.cpp`  
  ```
  进程内 `FScene` 表示（承接阶段 3 二进制）
  ```



## P1 — Mapping 求解主路径

- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Lighting.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightingSystem.h`  
  ```
  **注意**：此处 `FStaticLightingSystem` 是 **Lightmass 进程内** 类，勿与 Editor 的同名类混淆。
  搜：`ProcessTextureMapping`、`StaticLightingTask_*`、Photon / Radiosity / FinalGather 相关
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightingSystem.cpp`  
  ```
  任务队列 / 线程：跟 Mapping 处理与 Radiosity 调度，勿通读
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightingSystem.inl`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Mappings.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Mappings.cpp`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/TextureMapping.cpp`  
  ```
  **重点**：`RasterizeToSurfaceCacheTextureMapping`（约 267）；
  `ProcessTextureMapping`（约 692）→ Direct / Indirect 分支
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/TextureMappingSetup.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightmapData.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightmapData.cpp`  
  ```
  采样缓冲 / 量化结果结构（与 Public `ImportExport.h` 中 `FLightMapData2DData` 对照）
  ```
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Exporter.h`
- [x] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Exporter.cpp`  
  ```
  求解结果写回 Swarm 通道（供阶段 5 Editor Import）
  ```



## P2 — 光照算法与加速结构

- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/FinalGather.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/PhotonMapping.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Radiosity.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/MonteCarlo.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/MonteCarlo.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/GatheredLightingSample.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightingCache.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightingCache.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Collision.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Collision.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Embree.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Embree.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightingMesh.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/LightingMesh.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/StaticMesh.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/StaticMesh.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Raster.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/Texture.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/Lighting/BuildOptions.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Mesh.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Mesh.cpp`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Material.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/ImportExport/Material.cpp`



### LightmassCore（支撑）

- [ ] `Engine/Source/Programs/UnrealLightmass/Private/LightmassCore/LMCore.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/LightmassCore/Misc/LMThreading.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/LightmassCore/Math/LMMath.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Private/LightmassCore/Math/LMOctree.h`  
  ```
  其余 `LightmassCore/Math|Misc` 按需对照
  ```



### 契约头（与阶段 3 对照）

- [ ] `Engine/Source/Programs/UnrealLightmass/Public/ImportExport.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/SceneExport.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/MeshExport.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/Public/MaterialExport.h`
- [ ] `Engine/Source/Programs/UnrealLightmass/UnrealLightmass.Build.cs`  
  ```
  （可选）
  ```



## P3 — CPU 旁路几何 / 体积（非 2D LM 主干）

- [ ] `.../Lighting/BSP.*`、`Landscape.*`、`FluidSurface.*`
- [ ] `.../Lighting/SampleVolume.cpp`、`AdaptiveVolumetricLightmap.cpp`
- [ ] `.../Lighting/PrecomputedVisibility.cpp`、`VolumeDistanceField.cpp`
- [ ] `Public/FluidExport.h`、`UnrealLightmass.Target.cs`

---



# 4b — GPULightmass



## P0 — 模块注册与设置

- [x] `Engine/Plugins/Experimental/GPULightmass/GPULightmass.uplugin`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Public/GPULightmassModule.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/GPULightmassModule.cpp`  
  ```
  **重点**：`RegisterImplementation(FName("GPULightmass"), ...)`（约 35）
  ```
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Public/GPULightmassSettings.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/GPULightmassSettings.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/GPULightmass.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/GPULightmass.cpp`  
  ```
  `FGPULightmass : IStaticLightingSystem`；Component 注册钩子、`EditorTick`
  ```
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/GPULightmassCommon.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/GPULightmass.Build.cs`  
  ```
  （可选）
  ```



## P1 — 场景 + Tile 路径追踪主路径

- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/Scene.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/Scene.cpp`  
  ```
  大文件：搜 Lightmap / EncodeTextures / Tile；跟构建与写回边界（约 3004 `FLightMap2D::EncodeTextures`）
  ```
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/StaticMesh.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/StaticMesh.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/Lights.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/Lights.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/GeometryInterface.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/GeometryInterface.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapRenderer.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapRenderer.cpp`  
  ```
  `AddRequest` / GBuffer / `Finalize` / `BackgroundTick`
  ```
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapRayTracing.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapRayTracing.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapStorage.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapStorage.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapTilePool.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapTilePool.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapEncoding.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapEncoding.cpp`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapGBuffer.h`
- [x] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapGBuffer.cpp`



### 核心着色器

- [x] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/LightmapPathTracing.usf`
- [x] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/LightmapEncoding.ush`
- [x] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/LightmapCommon.ush`
- [x] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/LightmapOutput.usf`
- [x] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/LightmapGBuffer.usf`
- [x] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/LightmapBufferClear.usf`
- [x] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/BatchedTiles.ush`



## P2 — 降噪 / 预览 / 缓存

- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapDenoising.h`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapDenoising.cpp`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapPreviewVirtualTexture.h`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/LightmapPreviewVirtualTexture.cpp`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/IrradianceCaching.h`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/IrradianceCaching.cpp`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Shaders/Private/IrradianceCachingCommon.ush`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/Scene/InstancedStaticMesh.*`
- [ ] `Engine/Plugins/Experimental/GPULightmass/Source/GPULightmass/Private/EntityArray.h`



## P3 — 体积 / Editor / 阴影图

- [ ] `.../Private/VolumetricLightmap.*`、`VolumetricLightmapVoxelization.h`
- [ ] `Shaders/Private/VolumetricLightmapVoxelization.usf`、`StaticShadowDepthMap.usf`
- [ ] `Source/GPULightmassEditor/...`（编辑器模块注册）

---



## 模块汇总


| 轨道  | 区域                 | 目录                                                                 |
| --- | ------------------ | ------------------------------------------------------------------ |
| 共享  | Interface          | `Engine/Source/Runtime/Engine/.../StaticLightingSystemInterface.*` |
| 4a  | Launch / CPUSolver | `UnrealLightmass/Private/Launch`、`CPUSolver`                       |
| 4a  | ImportExport       | `UnrealLightmass/Private/ImportExport`                             |
| 4a  | Lighting           | `UnrealLightmass/Private/Lighting`                                 |
| 4a  | Core               | `UnrealLightmass/Private/LightmassCore`                            |
| 4a  | Public             | `UnrealLightmass/Public`                                           |
| 4b  | Runtime 模块         | `Plugins/.../GPULightmass/Source/GPULightmass`                     |
| 4b  | Shaders            | `Plugins/.../GPULightmass/Shaders`                                 |
| 4b  | Editor             | `Plugins/.../GPULightmassEditor`                                   |


完整路径见 [FILE_INDEX.txt](./FILE_INDEX.txt)。