# 效能瓶頸
效能瓶頸大多來自渲染
- 過大的 texture
- 過於複雜的 mesh
- shader 計算過慢
- 或是沒有很好利用 batching、culling、LOD

另一方面，也有可能是
- 使用太多 mesh collider，讓物理計算過慢
- 實現需求的代碼每一個 frame 消耗太多 cpu 時間

# 為什麼我的 CPU 程式碼很慢
- GC
- compiler 生成的機器碼不是最優的
- 沒有充分的利用 CPU 核心 : 雖然現在低端設備具備多核心處理器，但大多時候還是只有使用 main thread 
- 數據並非 cache-friendly : 如果發生 cache miss 就必須從 main memory 讀取資料，這比 cache 慢很多
> [!NOTE]
> 遵循 principle of locality 來設計資料結構
> - Temporal locality : 最近引用到的容易被再度引用，也就是儘可能使用同一份資料
> - Spatial locality : 最近引用到的位置周圍容易也被引用到，也就是盡可能使用連續記憶體區塊
- 程式碼並非 cache-friendly : 當程式執行時，若程式碼尚未存放在快取中，就必須從系統記憶體載入
> [!NOTE]
> 指的是指令快取（instruction cache）  
> 其中一種策略是盡量減少函式的呼叫點，以降低從系統記憶體載入的頻率
- 程式碼過於抽象
  - 經常依賴動態記憶體配置 (new) ，如果沒有 GC 會需要手動管理，提高複雜度
  - 一定程度的阻礙編譯器內聯 (inlining)，生成的機器碼可能不是最好的
  - 可能存在隱藏共享的狀態，例如 Singleton，要做到既安全又高效的多執行緒也變得更困難
  - 多型容易導致資料與程式碼分散，通常也會變得不利於快取使用（cache-unfriendly）
  - 抽象層的性能損耗，例如動態分派（Dynamic dispatch），容易分散在各個地方，導致整體執行速度變慢，而很難找出明確的瓶頸來進行優化

# Unity 的具體例子
- 儘管 C# 允許創建 manually-allocated 物件 (不被 GC 管理的)，但 Unity 大多還是使用 C# class
> [!NOTE]
> 常見的解決方案是物件池
- C# 通常使用 Mono Compiler 編譯，也可以使用 IL2CPP 來提高性能，但會讓模組支援變得更困難
> [!NOTE]
> - Mono Compiler : 採用 JIT（Just-In-Time）編譯，在運行時將程式碼轉為機器碼
>   - 優點：開發期編譯速度快，支援動態程式碼載入（利於模組化）
>   - 缺點：執行效能較低，且無法跨平台預編譯優化
> - IL2CPP : 將 C# 的 IL（Intermediate Language） 轉為 C++ 程式碼，再透過原生編譯器（如 GCC、MSVC）生成機器碼
>   - 優點
>     - 效能接近原生程式（AOT 編譯，優化更徹底）
>     - 支援平台更多（如 WebGL、iOS 等禁止 JIT 的環境）
>   - 缺點
>     - 建置時間大幅增加（需多一步 C++ 編譯）
>     - 失去動態程式碼載入能力（影響模組熱更新）
- Unity 專案基本上都是在 main thread 執行，因為
  - Unity 的事件函數，像是 Update()，都是在 main thread 執行
  - 大多數 Unity APIs 只能在 main thread 呼叫
- 傳統的 Unity 專案容易導致資料建構成隨機對象分散在記憶體中，使得快取使用率低，這也是因為
  - GameObject 和 Componet 都是單獨分配的，因此經常在不同的記憶體區塊中
- 傳統的 Unity 專案容易導致程式碼是 cache-unfriendly
  - 傳統的 C# 和 Unity API 鼓勵使用物件導向，導致大量的方法和對象內存分散
  - 每個 MonoBehaviour 的事件函數都是單獨呼叫的，而且不一定會按照類型分組
- 傳統 C# 和許多 Unity API 的物件導向風格通常會導致高度抽象的解決方案 
