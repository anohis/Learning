# ISystem
ISystem 有三個方法
- OnCreate : System 被創建時呼叫
- OnDestroy : System 被摧毀時呼叫
- OnUpdate :
  默認情況下，每一幀都會呼叫 OnUpdate
  但可以利用 SystemState.RequireForUpdate 來限制必須存在滿足條件的 Entity 存在時才執行 OnUpdate

> [!NOTE]
> 建議所有方法都加上 [BurstCompile] 提升效能

# SystemState

# World
