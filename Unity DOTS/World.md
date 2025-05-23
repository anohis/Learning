# Concepts
一個 world 是 entity 的集合，一個 entity 在 world 內是唯一的，可以使用 EntityManager 來創建/銷毀  
world 內還有一組 system，system 只能存取相同 world 內的 entity  

# Initialization
預設進入遊戲就會創建 world，以及包含在內的 system    
如果希望手動控制 system 的生成，請實作 ICustomBootstrap，並使用以下 define
- #UNITY_DISABLE_AUTOMATIC_SYSTEM_BOOTSTRAP_RUNTIME_WORLD
- #UNITY_DISABLE_AUTOMATIC_SYSTEM_BOOTSTRAP_EDITOR_WORLD
- #UNITY_DISABLE_AUTOMATIC_SYSTEM_BOOTSTRAP

### Manual World Creation
```c#
var world = DefaultWorldInitialization.Initialize("Custom World");
```
DefaultWorldInitialization.Initialize 會完整的創建一個預設世界，大致包含  
- InitializationSystemGroup
  - BeginInitializationEntityCommandBufferSystem
  - CopyInitialTransformFromGameObjectSystem
  - SubSceneLiveLinkSystem
  - SubSceneStreamingSystem
  - EndInitializationEntityCommandBufferSystem
- SimulationSystemGroup
  - BeginSimulationEntityCommandBufferSystem
  - TransformSystemGroup
    - EndFrameParentSystem
    - CopyTransformFromGameObjectSystem
    - EndFrameTRSToLocalToWorldSystem
    - EndFrameTRSToLocalToParentSystem
    - EndFrameLocalToParentSystem
    - CopyTransformToGameObjectSystem
  - LateSimulationSystemGroup
  - EndSimulationEntityCommandBufferSystem
- PresentationSystemGroup
  - BeginPresentationEntityCommandBufferSystem
  - CreateMissingRenderBoundsFromMeshRenderer
  - RenderingSystemBootstrap
  - RenderBoundsUpdateSystem
  - RenderMeshSystem
  - LODGroupSystemV1
  - LodRequirementsUpdateSystem
  - EndPresentationEntityCommandBufferSystem

```C#
var world = new World("Custom World");
var systems = new [] { typeof(FooSystem), typeof(BarSystem) };
DefaultWorldInitialization.AddSystemsToRootLevelSystemGroups(world, systems);
ScriptBehaviourUpdateOrder.AppendWorldToCurrentPlayerLoop(world);
```
可以自己創建 World，並使用 DefaultWorldInitialization.AddSystemsToRootLevelSystemGroups，此時會包含
- InitializationSystemGroup
- SimulationSystemGroup
  - FooSystem
  - BarSystem
- PresentationSystemGroup

```C#
var world = new World("DotsFisher");
var simulationSystemGroup = world.GetOrCreateSystemManaged<SimulationSystemGroup>();
var system = world.CreateSystemManaged<TestSystem2>();
simulationSystemGroup.AddSystemToUpdateList(system);
ScriptBehaviourUpdateOrder.AppendWorldToCurrentPlayerLoop(world);
```
想更加控制創建過程，可以使用 CreateSystemManaged 創建 System  
由於 AppendWorldToCurrentPlayerLoop 綁定 SimulationSystemGroup，因此必須要創建 SimulationSystemGroup 並且把 system 加到 simulationSystemGroup 之中




