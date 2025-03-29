# Dynamic Programming
適用範圍
- 最優子結構 (Optimal substructure): 如果可以從某個問題的子問題的最優解建構出一個最優解，則稱該問題具有最優子結構
  - 遞迴 
- 重疊子問題 (Overlapping Subproblems): 如果問題可以分解為多次重複使用的子問題，則稱該問題具有重疊子問題
  - 記憶化 (Memoization)
 
# [Medium] Longest Palindromic Substring
https://leetcode.com/problems/longest-palindromic-substring/description/
> [!NOTE]
> 一個字串是不是迴文可以拆解為
> - 左右兩個字母是否相同，以及
> - 剩下的字串是不是迴文
>
> 可以寫作
>
> $$
> dp[start][end] = (s[start]==s[end]) \quad and \quad dp[start+1][end-1]
> $$
>
> 判斷結果可以儲存起來以加速判斷速度

> [!NOTE]
> 另外有 [Expand Around Center](https://github.com/anohis/Learning/blob/main/LeetCode/Expand%20Around%20Center.md#medium-longest-palindromic-substring) 可以更快取得結果
