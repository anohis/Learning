# ISystem
ISystem 有三個方法
- OnCreate : System 被創建時呼叫
- OnDestroy : System 被摧毀時呼叫
- OnUpdate :  
  默認情況下，每一幀都會呼叫 OnUpdate  
  但可以利用 SystemState.RequireForUpdate 來限制必須存在滿足條件的 Entity 存在時才執行 OnUpdate  

> [!NOTE]
> 建議所有方法都加上 [BurstCompile] 提升效能

實作 ISystem 需要使用 partial struct

```C#
public partial struct RotationSystem : ISystem
{
    [BurstCompile]
    public void OnCreate(ref SystemState state)
    {
        state.RequireForUpdate<ExecuteMainThread>();
    }

    [BurstCompile]
    public void OnUpdate(ref SystemState state)
    {
    }
}
```

# Query
想要取得所有指定的 component 有以下方法

### SystemAPI.Query<RefRW<LocalTransform>, RefRO<RotationSpeed>>()
- RefRW : Read/Write
- RefRO : Read only
```C#
foreach (var (transform, speed) in SystemAPI.Query<RefRW<LocalTransform>, RefRO<RotationSpeed>>())
{
    transform.ValueRW = transform.ValueRO.RotateY(speed.ValueRO.RadiansPerSecond * deltaTime);
}
```

### Job Execute
```C#
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
  
### IAspect
IAspect 會包裝所需要的 component，SystemAPI.Query 會查詢在 IAspect 之中的成員  
實作 IAspect 需要使用 partial struct
```C#
foreach (var movement in SystemAPI.Query<VerticalMovementAspect>())
{
    movement.Move(elapsedTime);
}

readonly partial struct VerticalMovementAspect : IAspect
{
    readonly RefRW<LocalTransform> m_Transform;
    readonly RefRO<RotationSpeed> m_Speed;

    public void Move(double elapsedTime)
    {
        m_Transform.ValueRW.Position.y = (float)math.sin(elapsedTime * m_Speed.ValueRO.RadiansPerSecond);
    }
}
```

# SystemState

# World
