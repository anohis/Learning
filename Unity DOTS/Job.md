# IJobEntity
定義一個 Job 時需要使用 partial struct  
並且需要添加 [BurstCompile]
```C#
    [BurstCompile]
    partial struct RotateAndScaleJob : IJobEntity
    {
    }
```

# Query 
生成代碼的過程中，會根據 Job 中的 Execute 方法參數生成 Query  
其中 ref 是 read、write，in 是 read only  
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
