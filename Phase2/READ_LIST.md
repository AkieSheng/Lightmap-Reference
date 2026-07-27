# Phase 2 Read List

优先级：P0 必读 → P1 场景/物体参数 → P2 灯光与工具 → P3 桥接下一阶段 / 可选。

路径均相对 `Core/Phase2/`。

---

## P0 — 参数契约

- [x] `Engine/Source/Runtime/Engine/Classes/Engine/EngineTypes.h`  
  ```
  定位：
  - `ELightingBuildQuality`（约 1006）
  - `FLightmassLightSettings` / `FLightmassPointLightSettings` / `FLightmassDirectionalLightSettings`（约 2349–2397）
  - `FLightmassPrimitiveSettings`（约 2401）
  - （对照 Phase1）`FMeshBuildSettings` 与 LM UV 字段
  ```
- [x] `Engine/Source/Runtime/Engine/Classes/GameFramework/WorldSettings.h`  
  ```
  `EVolumeLightingMethod`、`FLightmassWorldInfoSettings`（约 55 起）；
  `AWorldSettings::LightmassSettings`（约 672）
  ```
- [x] `Engine/Source/Runtime/Engine/Classes/Engine/StaticMesh.h`  
  ```
  （承接 Phase1）`LightMapCoordinateIndex`、`LightMapResolution`
  ```

---



## P1 — 场景级 / 物体级参数落点



### WorldSettings（世界质量总开关）

- [x] `Engine/Source/Runtime/Engine/Private/WorldSettings.cpp`  
  ```
  搜 `LightmassSettings`：属性可见性（AO / Volumetric vs Sparse）、`PostEditChange` 中的 Clamp（约 863–877）
  ```



### Component 覆盖（分辨率 + Primitive Lightmass）

- [x] `Engine/Source/Runtime/Engine/Classes/Components/StaticMeshComponent.h`  
  ```
  `bOverrideLightMapRes`、`OverriddenLightMapRes`、`LightmassSettings`；
  `GetLightMapResolution` / `GetStaticLightMapResolution` / `HasValidSettingsForStaticLighting`
  ```
- [x] `Engine/Source/Runtime/Engine/Private/Components/StaticMeshComponent.cpp`  
  ```
  **重点**：`GetLightMapResolution`（约 2650）：Override → 资产 `GetLightMapResolution()`；
  `HasValidSettingsForStaticLighting`（约 2733）；
  `OverriddenLightMapRes` 对齐到 4 的倍数（约 1955）
  ```



### Lightmass Volume / 覆盖对象

- [x] `Engine/Source/Runtime/Engine/Classes/Lightmass/LightmassImportanceVolume.h`
- [x] `Engine/Source/Runtime/Engine/Private/LightmassImportanceVolume.cpp`  
  ```
  Importance Volume：框定光子/体积采样重点区域（编辑后触发重构建提示）
  ```
- [x] `Engine/Source/Runtime/Engine/Classes/Lightmass/LightmassPrimitiveSettingsObject.h`
- [x] `Engine/Source/Runtime/Engine/Private/LightmassPrimitiveSettingsObject.cpp`  
  ```
  可复用的 `FLightmassPrimitiveSettings` 容器
  ```
- [x] `Engine/Source/Runtime/Engine/Classes/Lightmass/LightmassPortal.h`
- [x] `Engine/Source/Runtime/Engine/Classes/Components/LightmassPortalComponent.h`
- [x] `Engine/Source/Runtime/Engine/Private/Components/PortalComponent.cpp`  
  ```
  Portal：引导间接光穿过开口；读 Actor + Component 结构即可
  ```

---



## P2 — 灯光 Lightmass 设置 + 分辨率工具

- [ ] `Engine/Source/Runtime/Engine/Classes/Components/LightComponent.h`  
  ```
  `GetLightmassSettings()` 虚接口
  ```
- [ ] `Engine/Source/Runtime/Engine/Classes/Components/LocalLightComponent.h`  
  ```
  `FLightmassPointLightSettings LightmassSettings`
  ```
- [ ] `Engine/Source/Runtime/Engine/Classes/Components/DirectionalLightComponent.h`  
  ```
  `FLightmassDirectionalLightSettings`（含 `LightSourceAngle`）
  ```
- [ ] `Engine/Source/Editor/LevelEditor/Public/LightmapResRatioAdjust.h`
- [ ] `Engine/Source/Editor/LevelEditor/Private/LightmapResRatioAdjust.cpp`  
  ```
  `ApplyRatioAdjustment`：批量改 StaticMesh / BSP 的 Lightmap 分辨率（读 `GetStaticLightMapResolution` → 乘 Ratio → 写 Override）
  ```

---



## P3 — 参数如何进入静态光照抽象（桥接阶段 3）

- [ ] `Engine/Source/Runtime/Engine/Public/StaticLighting.h`  
  ```
  `FStaticLightingMesh`、`FStaticLightingMapping`、`FStaticLightingTextureMapping`
  （构造参数：`SizeX/Y`、`LightmapTextureCoordinateIndex`）
  ```
- [ ] `Engine/Source/Runtime/Engine/Public/StaticLightingBuildContext.h`
- [ ] `Engine/Source/Runtime/Engine/Private/StaticLightingBuildContext.cpp`  
  ```
  `SetImportanceBounds`、`ShouldIncludeActor` / `ShouldIncludeLevel`、MapBuildData Registry 选择
  ```
- [ ] `Engine/Source/Runtime/Engine/Public/StaticMeshLight.h`
- [ ] `Engine/Source/Runtime/Engine/Private/StaticMeshLight.cpp`  
  ```
  **重点**：用 `GetLightMapResolution` + `GetLightMapCoordinateIndex` 创建 Texture Mapping（约 341–386、436 一带）；
  `LightmassSettings` 的 Emissive/DiffuseBoost 转发
  ```



### 可选（体积细节，非 2D Lightmap 主干）

- [ ] `Engine/Source/Runtime/Engine/Classes/Lightmass/LightmassCharacterIndirectDetailVolume.h`
- [ ] `Engine/Source/Runtime/Engine/Private/LightmassCharacterIndirectDetailVolume.cpp`  
  ```
  间接光采样密度体积；与 2D Lightmap 平行，了解即可
  ```

---



## 模块汇总


| 模块 / 区域                     | 目录                                                 | 文件数 |
| --------------------------- | -------------------------------------------------- | --- |
| EngineTypes / StaticMesh    | `Engine/Source/Runtime/Engine/Classes/Engine/`     | 2   |
| WorldSettings               | `.../GameFramework/` + `Private/WorldSettings.cpp` | 2   |
| StaticMeshComponent         | `.../Components/StaticMeshComponent.*`             | 2   |
| Lightmass Volumes / Objects | `.../Classes/Lightmass/` + 对应 Private              | 7   |
| Light Components            | `LightComponent` / `Local` / `Directional`         | 3   |
| StaticLighting 抽象           | `StaticLighting*` / `StaticMeshLight*`             | 5   |
| LevelEditor 工具              | `LightmapResRatioAdjust.*`                         | 2   |


完整路径见 [FILE_INDEX.txt](./FILE_INDEX.txt)。