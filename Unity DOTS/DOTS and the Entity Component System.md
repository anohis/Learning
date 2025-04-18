# The C# job system
job system 提供簡單有效的方法來編寫多執行緒程式碼  
- job system 維護一個工作執行緒池，每個額外的核心都有一個 worker thread (例如一個八核心上有 1 個 main thread 和 7 個 worker thread)
- worker thread 的執行單元稱作 job，當 worker thread 閒置時，他會從 job queue 取得一個 job 執行
- 一個 job 只要在一個 worker thread 上被執行，就會直到結束為止，也就是說一個 job 不會被搶占

```C#
// A simple example job that multiplies the
// elements of two arrays.
// Implementing IJob makes this struct a job type.
struct MyJob : IJob
{
    // A NativeArray is “unmanaged”, meaning it
    // isn’t garbage collected.
    public NativeArray<float> Input;
    public NativeArray<float> Output;
    // The Execute method is called when the
    // job system executes this job.
    public void Execute()
    {
        // Multiply every value in Output by the
        // corresponding value in the Input array.
        for (int i = 0; i < Input.Length; i++)
        {
            Output[i] *= Input[i];
        }
    }
}

var job = new MyJob();
JobHandle handle = job.Schedule();
handle.Complete();
```

### 安排和完成 job
- 一個 job 只能在 main thread 進入排程，不能從其他的 job
- 只有在 main thread 可以呼叫 JobHandle.Complete()，且一旦呼叫就會導致阻塞，直到 job 完成
- 一個 job 結束後，可以保證其上面的資料是安全的

### 安全檢查和依賴性
- 為了確保隔離，每個 job 都有私有的資料，main thread 和其他 job 不能訪問
- 如果多個 job 共享相同資料且並行會導致 Race Conditions，因此當排程一個可能造成衝突的 job 時，安全檢查會拋出例外
- 在排程一個 job 時，可以宣告依賴的 job，此時 job 會等到依賴的 job 完成後才會執行
- 完成一個 job 表示其直接或間接依賴的 job 也是完成的
> [!NOTE]
> 只要有 "可能" Race Conditions 就會拋出例外
> ```C#
> NativeArray<float> data = new NativeArray<float>(10, Allocator.TempJob);
> var jobA = new JobA { Data = data };  
> JobHandle handleA = jobA.Schedule();  
> var jobB = new JobB { Data = data };  
> JobHandle handleB = jobB.Schedule(); // throw InvalidOperationException
> handleA.Complete();  
> handleB.Complete();  
> data.Dispose();  
> ```
> 正確方式
> ```C#
> JobHandle handleB = jobB.Schedule(handleA);  
> ```

> [!NOTE]
> Unity 不少 API 是使用 job 實現的，因此在 profiler 中也許可以看到

> [!NOTE]
> job 一般不會用在 IO 和網路傳輸，例如讀寫檔案  
> 因為通常這些操作會阻塞執行緒  
> 對於這類的需求請用異步或傳統的 C# 多執行緒功能

# The Burst compiler
Burst compiler 提供第三種編譯方案，專為資料導向 (DoD) 和 job system 優化   
在重度計算的情況下執行效率會比 Mono 或 IL2CPP 更好  
但僅可以編譯一小部分的 C#，大部分 C# 無法被編譯  
最大的限制是無法訪問託管物件 (Managed object)

```C#
// The BurstCompile attribute marks this job to be Burst-compiled.
[BurstCompile]
struct MyJob : IJob
{
    public void Execute()
    {
    }
}
```

Burst 的效能提升主要來自於以下幾個技術  
- SIMD（單指令多資料）：這是一種能夠同時對多個資料元素執行相同操作的技術，大幅提高處理效率
- 更佳的別名分析（aliasing awareness）：當兩個或多個指標或參考指向相同的記憶體位置時，稱為別名。Burst 更能正確理解這些情況，進而做出更有效的最佳化

# Collections
提供非託管的集合結構，例如 list，這些結構針對 job、Burst 進行優化  
由於非託管因此需要主動呼叫 Dispose() 釋放資源  
集合的型別可以區分為以下
- 以 Native 開頭命名的型別會執行安全檢查，當遇到以下情況會拋出例外
  - 如果沒有 Dispose
  - 如果在 job 中以非執行緒安全的方式使用
- 以 Unsafe 開頭命名的型別不會執行安全檢查
- 剩下的型別都是小型結構，沒有指針因此也不需要分配與釋放，也不存在多執行緒安全問題

> [!NOTE]
> 通常 native 型別都有等價的 unsafe 型別，例如 NativeList 和 UnsafeList  
> 但基於安全考量，建議使用 native 型別

# Mathematics
Mathematics package 是一個針對 job、Burst 進行優化的 C# 數學庫  
提供
- 向量或矩陣，例如 float3, quaternion, float3x3
- 許多數學方法和運算子遵循 HLSL（High-Level Shader Language）著色器語言的慣例
- 針對多種方法和運算子，Burst 提供了特殊的優化鉤子（optimization hooks）

> [!NOTE]
> 雖然 UnityEngine.Mathf 也可以在 Burst 使用，但 Unity.Mathematics 表現得更好

# Entities (ECS)
一個 entity 由 component 組成，其中 component 通常是一個 C# struct  
component 通常不會有自己的方法，取而代之的是 system  
system 有 update 方法會在每個 frame 呼叫，此時 system 會讀取並修改 component  

### Archetypes
ECS 中，由相同 component 組成的 entity 會被儲存在同一個 archetypes  
對 entity 移除或添加 component 會使得 entity 從一個 Archetypes 轉移到另一個 archetypes  

### Chunks
一個 archetypes 內部會將 entity 和 component 儲存在一個記憶體區塊中，稱作 chunk  
每個 chunk 最多儲存 128 個 entity，以及他們的 component 會依照型別各自儲存在對應的陣列中  
chunk 內的陣列總是緊密排列的
- 當新的 entity 加入 chunk 時，會選擇第一個空間儲存
- 當 entity 離開 chunk 時，最後一個 entity 會移動到缺口中

### Queries
使用 archetype 和 chunk 的主要優點是能高效的查詢和迭代 entity  
如果想要找到擁有指定 component 的所有 entity，首先是查詢符合條件的 archetypes，然後迭代他們的 chunk
- 因為 component 是儲存在緊密排列的陣列中，可以很大程度避免 cache miss
- 因為 archetype 在大部分時間是穩定的，因此符合條件的 archetype 通常能被 cache

### Job system integration
只要 entity 和 component 是非託管的，他們就可以在 Burst 編譯的 job 中被存取  
提供兩個特殊的型別用來訪問 entity : IJobChunk、IJobEntity 

### Subscenes and baking
ECS 使用 subscene 取代 scene 來管理遊戲內容，因為 scene 並不相容於 ECS  
雖然 entity 不能直接包含在 Unity 場景中，但一項名為 baking 的功能允許從場景載入 entity 並轉換 GameObject 和 MonoBehaviour 為 entity 和 component  
可以將 subscenes 視為嵌套在其他場景中的場景，每當你編輯 subscenes 時，baking 都會重新執行  
對於 subscenes 中的每個 GameObject，baking 創建一個 entity，這些 entity 會被序列化儲存到檔案中  
當 subscenes 在運行時加載時，實際加載的是這些 entity，而非原始的 GameObject  
哪些 component 會被加入 baking 後的 entity，取決於與 GameObject 組件關聯的 baker  
例如，與標準圖形組件（如 MeshRenderer）關聯的 baker，會為 entity 添加與圖形相關的 component  
而對於自訂的 MonoBehaviour 類型，可以自行定義 baker，來控制 baking 後的 entity 會附加哪些額外的 component

```C#
// This entity component type represents an energy shield with hit points,
// maximum hit points, recharge delay, and recharge rate.
public struct EnergyShield : IComponentData
{
    public int HitPoints;
    public int MaxHitPoints;
    public float RechargeDelay;
    public float RechargeRate;
}
// A simple example authoring component.
// An authoring component is just an ordinary MonoBehaviour
// that has a defined Baker class.
public class EnergyShieldAuthoring : MonoBehaviour
{
    public int MaxHitPoints;
    public float RechargeDelay;
    public float RechargeRate;
    // The baker for our EnergyShield authoring component.
    // This baker is run once for every EnergyShieldAuthoring
    // instance that’s attached to any GameObject in a subscene.
    class Baker : Baker<EnergyShieldAuthoring>
    {
        public override void Bake(EnergyShieldAuthoring authoring)
        {
            // The TransformUsageFlags specify which
            // transform components the entity should have.
            // The None flag means that it doesn’t need transforms.
            var entity = GetEntity(TransformUsageFlags.None);
            // This simple baker adds just one component to the entity.
            AddComponent(entity, new EnergyShield
            {
                HitPoints = authoring.MaxHitPoints,
                MaxHitPoints = authoring.MaxHitPoints,
                RechargeDelay = authoring.RechargeDelay,
                RechargeRate = authoring.RechargeRate,
            });
        }
    }
}
```

baking 有效地將「創作數據」（你在編輯器中修改的 GameObject）與「運行時數據」（baking 後的 entity）分離開來  
舉例來說，你可以編寫程式碼，在 baking 過程中動態生成數據，從而避免在遊戲運行時消耗效能

# Streaming
對於大型場景來說，能隨著玩家或攝影機高速加載/卸載大量環境物件非常重要，這種技術也被稱作 streaming  
entity 比 gameobject 更加適合 streaming，因為使用的記憶體更少、處理開銷更低，而且能更高效的序列化/反序列化

# EntityComponentSystemSamples Github
[EntityComponentSystemSamples](https://github.com/Unity-Technologies/EntityComponentSystemSamples/tree/master)  
[Get acquainted with DOTS](https://learn.unity.com/project/basics-dots-jobs-entities?uv=2022.3)

# Entities Graphics
提供透過 URP、HDRP 渲染物件的 component 和 system，底層基於 BatchRendererGroup 實現

# Physics
unity 的物理表層 API 會保持一致，僅替換內部實現的方式  
Havok Physics 套件提供基於 Havok 的物理引擎，該引擎已被眾多業界頂尖的 3A 級遊戲所採用

# Netcode for Entities
Netcode for Entities 套件是 Unity 提供的兩個網路代碼解決方案之一 (另一個是 Netcode for GameObjects)  
此套件使用一個 authoritative server，並支援 client 預測，使其更適合快節奏的競技遊戲  

### Authoritative server
authoritative server 全權負責執行完整的遊戲模擬，並決定遊戲中的所有狀態變化  
client 僅需將玩家輸入傳送至 server，由 server 統一更新遊戲模擬狀態，再將最新的遊戲狀態快照回傳給 client

### Client-side prediction 
由於資料與狀態的網路傳輸需要時間，因此 client 總是比 server 慢  
client 會嘗試預測未來幾毫秒的遊戲狀態，假如預測夠精準將會獲得 0 延遲的體驗

# Character Controller
CharacterController 套件提供基於 ECS 實現的第一和第三人稱的角色控制器，此控制器會使用 Unity Physics 和 Netcode for Entities

# Evaluating DOTS for your project 
如果你的程式碼陷入 CPU 瓶頸，可以考慮使用 job 重新實現功能  
即使專案並沒有使用 DOTS 架構，job 通常也能相對輕鬆地整合到大多數現有專案中  
這種情況在 Entities 套件上就較不適用，雖然有時可以選擇性地整合 Entities 來實現特定功能，但 ECS 架構往往會要求整個專案遵循其特定的程式碼結構  
可能需要開新的 Entities 專案的理由有
- 專案內有大量靜態物件
- 專案內有大量動態物件有著需要執行大量運算的行為，例如尋路
- 更偏好用 ECS 的方式設計資料結構與程式碼，這種方式比較容易分析和找到瓶頸
- 專案屬於線上多人競技類型，並且需要使用預測來提升玩家體驗，例如 FPS 遊戲

另一方面，也有許多遊戲的瓶頸是 GPU，此時 DOTS 的幫助有限，不過若 DOTS 能用更短的 CPU 時間內完成相同工作量，就能為其他功能保留更多效能餘裕
