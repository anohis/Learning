# Allocator
可以透過 Allocator 指定如何分配非託管的記憶體
- Allocator.Temp : 最快但時間也最短的，無法傳遞給 Job
- Allocator.TempJob : 可以傳遞到 Job 的短期分配器
- Allocator.Persistent : 無期限的，可以傳遞到 Job

### Allocator.Temp 
每幀開始時都會在主執行緒建立臨時分配器，並且在幀結束時釋放  
此外每個 Job 執行時也會建立，Job 結束時釋放  
因為記憶體會主動釋放，因此不需要手動釋放，也不會有任何效果

### Allocator.TempJob
分配器一旦建立後必須在 4 幀內釋放
對於 Native 結構，超過 4 幀會引發安全異常，Unsafe 結構則不會

### Allocator.Persistent 
由於可以無限期地保留記憶體，所以需要手動釋放  
如果資料依賴 Job，可以使用 Dispose(JobHandle) 等待 Job 完成才釋放
```C#
NativeArray<int> nums = new NativeArray<int>(10, Allocator.TempJob);
ExampleJob job = new ExampleJob { Nums = nums };
JobHandle handle = job.Schedule();
handle = nums.Dispose(handle);
```
