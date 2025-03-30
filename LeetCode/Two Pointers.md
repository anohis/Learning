# [Medium] Container With Most Water
https://leetcode.com/problems/container-with-most-water/description/
> [!NOTE]
> 暴力解會需要 $O(n^2)$，因此需要快速剃除不可能的組合  
> 可以觀察到容量計算取受限於最矮的那邊，因此如果下一個組合最矮的一邊不變，整體容量不可能變得更多  

# [Medium] 3Sum
https://leetcode.com/problems/3sum/description/
> [!Note]
> 固定其中一個變數可以將問題視為 [2Sum](https://github.com/anohis/Learning/blob/main/LeetCode/Hash%20Table.md#two-sum)  
> 但此時 Two Pointers 比 Hash Table 好的原因是兩者複雜度都是 O(N^2)，排序產生的消耗影響變小  
> 又 Two Pointers 可以提高搜尋效率，Hash Table 必須完整遍歷陣列  
> 綜合來看 Two Pointers 會是更優的選擇
