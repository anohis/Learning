# Create
```C#
var entity = state.EntityManager.CreateEntity();
state.EntityManager.AddComponentData(entity, directoryManaged);
```

# Instantiate
```
public void OnUpdate(ref SystemState state)
{
    Entity prefab = SystemAPI.GetSingleton<Spawner>().Prefab;
    var instances = state.EntityManager.Instantiate(prefab, 500, Allocator.Temp);
}
```

# Destroy
```C#
state.EntityManager.DestroyEntity();
```

從 EntityCommandBufferSystem 創建 CommandBuffer，呼叫 DestroyEntity 摧毀 Entity
```C#
public void OnUpdate(ref SystemState state)
{
    var ecbSingleton = SystemAPI.GetSingleton<BeginSimulationEntityCommandBufferSystem.Singleton>();
    var ecb = ecbSingleton.CreateCommandBuffer(state.WorldUnmanaged);

    foreach (var (transform, entity) in
             SystemAPI.Query<RefRW<LocalTransform>>()
                 .WithAll<RotationSpeed>()
                 .WithEntityAccess())
    {
        if (transform.ValueRO.Position.y < 0)
        {
            ecb.DestroyEntity(entity);
        }
    }
}
```
