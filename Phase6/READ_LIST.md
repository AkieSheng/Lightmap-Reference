# Phase 6 Read List

优先级：P0 必读 → P1 绑定与 VF → P2 Policy / BasePass 着色 → P3 调试与移动端可选。

路径均相对 `Core/Phase6/`。

---

## P0 — 运行时契约

- [x] `Engine/Source/Runtime/Engine/Classes/Engine/MapBuildDataRegistry.h`  
  ```
  `FMeshMapBuildData`：`LightMap` / `ShadowMap`（阶段 5 产物）
  ```
- [x] `Engine/Source/Runtime/Engine/Public/LightMap.h`  
  ```
  `FLightMap2D::GetInteraction` → `FLightMapInteraction`（贴图指针 + Scale/Add + CoordinateScale/Bias）
  ```
- [x] `Engine/Source/Runtime/Engine/Public/LightmapUniformShaderParameters.h`  
  ```
  `LightMapCoordinateScaleBias`、`ShadowMapCoordinateScaleBias`；Cluster / Scene 数据布局
  ```
- [x] `Engine/Source/Runtime/Engine/Classes/Engine/StaticMesh.h`  
  ```
  （对照阶段 1）`LightMapCoordinateIndex`：VF 取哪路 UV
  ```

---



## P1 — 场景绑定 + Vertex Factory 出 LM UV



### SceneProxy / LCI

- [x] `Engine/Source/Runtime/Engine/Public/SceneManagement.h`  
  ```
  **重点**：`FLightCacheInterface`（约 806）：`SetLightMap`、`GetLightMapInteraction`
  ```
- [x] `Engine/Source/Runtime/Engine/Public/StaticMeshSceneProxy.h`
- [x] `Engine/Source/Runtime/Engine/Private/StaticMeshSceneProxy.cpp`  
  ```
  LOD 实现 `FLightCacheInterface`；搜 `GetMeshMapBuildData`（约 2631 / 2694）、
  `GetLightMapCoordinateIndex`（约 549）、构造时如何 `SetLightMap`
  ```
- [x] `Engine/Source/Runtime/Engine/Private/MapBuildData.cpp`  
  ```
  运行时 `GetMeshBuildData` 查找（对照阶段 5 Allocate）
  ```
- [x] `Engine/Source/Runtime/Engine/Private/LightMap.cpp`  
  ```
  **重点**：`FLightMap2D::GetInteraction`（约 3250）；UniformBuffer / ResourceCluster（约 3483 一带）
  ```



### Vertex Factory

- [x] `Engine/Source/Runtime/Engine/Public/LocalVertexFactory.h`
- [x] `Engine/Source/Runtime/Engine/Private/LocalVertexFactory.cpp`  
  ```
  `GetLightMapCoordinateIndex`；顶点流如何声明 LightMap UV attribute
  ```
- [x] `Engine/Shaders/Private/LocalVertexFactory.ush`  
  ```
  **重点**：`ATTRIBUTE15` LightMapCoordinate（约 125）；
  `VertexFactoryGetInterpolantsVSToPS` 内 scale/bias（约 1120–1156）
  ```
- [x] `Engine/Shaders/Private/LocalVertexFactoryCommon.ush`  
  ```
  VF 公共辅助（随 LocalVertexFactory 对照）
  ```

---



## P2 — LightMap Policy + BasePass 采样混合



### C++ Policy

- [ ] `Engine/Source/Runtime/Renderer/Private/LightMapRendering.h`  
  ```
  `ELightMapPolicyType`（`LMP_HQ_LIGHTMAP` / `LQ` / DF Shadow 组合等）；
  `TLightMapPolicy`、`FUniformLightMapPolicy`
  ```
- [ ] `Engine/Source/Runtime/Renderer/Private/LightMapRendering.cpp`  
  ```
  `ShouldCompilePermutation` / `ModifyCompilationEnvironment`（约 439+）：
  如何打开 `HQ_TEXTURE_LIGHTMAP` / `LQ_TEXTURE_LIGHTMAP` 宏
  ```
- [ ] `Engine/Source/Runtime/Renderer/Private/BasePassRendering.h`  
  ```
  `TBasePass*Shader*Type<LightMapPolicyType>`：Policy 如何嵌进 BasePass shader 类型
  ```
- [ ] `Engine/Source/Runtime/Renderer/Private/BasePassRendering.cpp`  
  ```
  搜 `LightMapPolicy` / `FUniformLightMapPolicy`：绘制时选择哪种 Policy、绑定参数
  ```



### 像素着色器

- [ ] `Engine/Shaders/Private/LightmapData.ush`  
  ```
  Lightmap 纹理/系数解包数据结构
  ```
- [ ] `Engine/Shaders/Private/LightmapCommon.ush`  
  ```
  **重点**：`GetLightMapColorLQ`（约 78）、`GetLightMapColorHQ`（约 132）
  ```
- [ ] `Engine/Shaders/Private/BasePassPixelShader.usf`  
  ```
  `#include "LightmapCommon.ush"`（约 97）；
  `GetPrecomputedIndirectLightingAndSkyLight` 内 `HQ_TEXTURE_LIGHTMAP` / `LQ_TEXTURE_LIGHTMAP` 分支（约 718–737）；
  主路径如何把 `DiffuseIndirectLighting` 加进最终色（约 1359+）
  ```
- [ ] `Engine/Shaders/Private/MobileBasePassPixelShader.usf`  
  ```
  移动端 LQ 路径对照
  ```



### VT / Uniform 细节

- [ ] `Engine/Source/Runtime/Engine/Classes/VT/LightmapVirtualTexture.h`
- [ ] `Engine/Source/Runtime/Engine/Private/VT/LightmapVirtualTexture.cpp`  
  ```
  VT Lightmap 资源类型；与 `LIGHTMAP_VT_ENABLED` 分支对照
  ```
- [ ] `Engine/Source/Runtime/Engine/Private/LightmapUniformShaderParameters.cpp`  
  ```
  如何从 LCI / Cluster 填 Uniform
  ```

---



## P3 — 材质节点与调试

- [ ] `Engine/Source/Runtime/Engine/Public/Materials/MaterialExpressionLightmapUVs.h`  
  ```
  材质图中暴露 Lightmap UV（实现散落在 MaterialExpressions 大文件，原树按类名搜即可）
  ```
- [ ] `Engine/Source/Runtime/Renderer/Public/LightMapHelpers.h`
- [ ] `Engine/Source/Runtime/Renderer/Private/LightMapHelpers.cpp`
- [ ] `Engine/Source/Runtime/Renderer/Private/LightMapDensityRendering.h`
- [ ] `Engine/Source/Runtime/Renderer/Private/LightMapDensityRendering.cpp`
- [ ] `Engine/Shaders/Private/LightMapDensityShader.usf`  
  ```
  Lightmap 密度可视化（编辑器调试）
  ```

---



## 模块汇总（本 Phase 已收录）


| 区域                          | 目录                                 | 文件数（约） |
| --------------------------- | ---------------------------------- | ------ |
| Renderer Policy / BasePass  | `Source/Runtime/Renderer/Private/` | 8      |
| SceneProxy / LCI / LightMap | `Source/Runtime/Engine/...`        | 12     |
| Vertex Factory              | `LocalVertexFactory.*`             | 2      |
| Shaders                     | `Engine/Shaders/Private/`          | 7      |
| 材质节点                        | `MaterialExpressionLightmapUVs.h`  | 1      |


完整路径见 [FILE_INDEX.txt](./FILE_INDEX.txt)。

**刻意未收录：** 完整 `MaterialExpressions.cpp`、Nanite/RT hit 全套、Lumen 动态 GI（与预计算 LM 混合时在 BasePass 其它分支，本 Phase 以 Texture Lightmap 主干为准）。