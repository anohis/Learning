# IEnableableComponent 
IEnableableComponent 表示一個 Component 可以具有 enable/disable 狀態

```C#
struct RotationSpeed : IComponentData, IEnableableComponent
{
    public float RadiansPerSecond;
}
```

使用 SetComponentEnabled 可以設定 enable/disable
```C#
SetComponentEnabled<RotationSpeed>(entity, authoring.StartEnabled);
```

或是使用 ValueRW
```C#
public void OnUpdate(ref SystemState state)
{
    foreach (var rotationSpeedEnabled in
             SystemAPI.Query<EnabledRefRW<RotationSpeed>>()
                 .WithOptions(EntityQueryOptions.IgnoreComponentEnabledState))
    {
        rotationSpeedEnabled.ValueRW = !rotationSpeedEnabled.ValueRO;
    }
}
```
