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
