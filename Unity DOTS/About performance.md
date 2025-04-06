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
