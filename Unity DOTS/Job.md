# IJobEntity
IJobEntity 會按照 entity 迭代資料  
定義一個 Job 時需要使用 partial struct  
並且需要添加 [BurstCompile]

### Query 
生成代碼的過程中，會根據 Job 中的 Execute 方法參數生成 Query  
> [!NOTE]
> 建議 Execute 參數需要加上 ref/in 等修飾

```C#
[BurstCompile]
public void OnUpdate(ref SystemState state)
{
    var job = new RotateAndScaleJob
    {
        deltaTime = SystemAPI.Time.DeltaTime,
        elapsedTime = (float)SystemAPI.Time.ElapsedTime
    };
    job.Schedule();
}

[BurstCompile]
partial struct RotateAndScaleJob : IJobEntity
{
    public float deltaTime;
    public float elapsedTime;

    void Execute(ref LocalTransform transform, ref PostTransformMatrix postTransform, in RotationSpeed speed)
    {
        transform = transform.RotateY(speed.RadiansPerSecond * deltaTime);
        postTransform.Value = float4x4.Scale(1, math.sin(elapsedTime), 1);
    }
}
```

# IJobChunk 
IJobChunk 會按照 chunk 迭代資料

> [!NOTE]
> 考量底層優化，如果 chunk 內只執行一次迭代 entity 的話，建議使用 IJobEntity
> 滿足以下理由之一才應該考慮使用 IJobChunk
> - 並不需要迭代 entity，例如統計 chunk 訊息
> - chunk 內執行多次迭代 entity

### Implement IJobChunk
- 使用 EntityQuery 查詢  
  跟 IJobEntity 不同，IJobChunk 需要手動 Query   
  
> [!NOTE]
> 不要在 EntityQuery 查詢可選的 component，因為 EntityQuery 的條件是必要的  
> 此外，由於同一個 chunk 內的 component 組合是固定的，因此只需要檢查一次就好，而不必每個 entity 檢查  
> 檢查 component 存不存在可以使用 IsCreated 判斷
> ```C#
> NativeArray<Rotation> rotations = chunk.GetNativeArray(ref RotationTypeHandle);
> NativeArray<LocalToWorld> transforms = chunk.GetNativeArray(ref LocalToWorldTypeHandle);
> if (rotations.IsCreated && transforms.IsCreated)
> ```
  
- 使用 ComponentTypeHandle 定義需要訪問的 component  
  呼叫 SystemAPI.GetComponentTypeHandle<T>() 可以取得 ComponentTypeHandle
- 實作 void Execute(in ArchetypeChunk chunk, int unfilteredChunkIndex, bool useEnabledMask, in v128 chunkEnabledMask)
  - chunk : 當前的 chunk，可使用 chunk.GetNativeArray 取得指定的 component 陣列
  - unfilteredChunkIndex : 當前的 chunk 索引
  - useEnabledMask : 位元遮罩，第 N 個位元表示第 N 個 entity 符合 Query
  - chunkEnabledMask : 是否啟用 useEnabledMask，false 表示不需要檢查 useEnabledMask
    
> [!NOTE]
> 如果一個 component 是 IEnableableComponent，表示 component 可能是 disable，此時不應該處理此 component  
> 由於 IJobChunk 是針對整個 Chunk 的，所以需要利用 useEnabledMask 手動忽略 entity  
> 當有任一個 component 是 disable，chunkEnabledMask 將會是 true  
> 一個簡單的方式是使用 ChunkEntityEnumerator
> ```C#
> var enumerator = new ChunkEntityEnumerator(useEnabledMask, chunkEnabledMask, chunk.Count);
> while(enumerator.NextEntityIndex(out var i))
> ```
> 如果確定不存在 disable 的 component 也應該使用 Assert.IsFalse(useEnabledMask)，避免之後修改帶來的 bug

- 在 system.OnUpdate 加入排程  
  IJobChunk 並不會隱式傳遞  state.Dependency 的 JobHandle，因此需要手動管理

> [!NOTE]
> - job.Schedule : chunk 會被依序處理
> - job.ScheduleParallel : chunk 會被平行處理

```C#
[BurstCompile]
public void OnUpdate(ref SystemState state)
{
    var spinningCubesQuery = SystemAPI.QueryBuilder().WithAll<RotationSpeed, LocalTransform>().Build();

    var job = new RotationJob
    {
        TransformTypeHandle = SystemAPI.GetComponentTypeHandle<LocalTransform>(),
        RotationSpeedTypeHandle = SystemAPI.GetComponentTypeHandle<RotationSpeed>(true),
        DeltaTime = SystemAPI.Time.DeltaTime
    };

    state.Dependency = job.Schedule(spinningCubesQuery, state.Dependency);
}

[BurstCompile]
struct RotationJob : IJobChunk
{
    public ComponentTypeHandle<LocalTransform> TransformTypeHandle;
    [ReadOnly] public ComponentTypeHandle<RotationSpeed> RotationSpeedTypeHandle;
    public float DeltaTime;

    public void Execute(in ArchetypeChunk chunk, int unfilteredChunkIndex, bool useEnabledMask,
        in v128 chunkEnabledMask)
    {
        var transforms = chunk.GetNativeArray(ref TransformTypeHandle);
        var rotationSpeeds = chunk.GetNativeArray(ref RotationSpeedTypeHandle);
        for (int i = 0, chunkEntityCount = chunk.Count; i < chunkEntityCount; i++)
        {
            transforms[i] = transforms[i].RotateY(rotationSpeeds[i].RadiansPerSecond * DeltaTime);
        }
    }
}
```
