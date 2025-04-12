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
```C#
var world = new World("Custom World");
var systems = new [] { typeof(FooSystem), typeof(BarSystem) };
DefaultWorldInitialization.AddSystemsToRootLevelSystemGroups(world, systems);

ScriptBehaviourUpdateOrder.AppendWorldToCurrentPlayerLoop(world);
```

```
var world = DefaultWorldInitialization.Initialize("Custom World");
```


