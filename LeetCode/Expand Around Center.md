# Expand Around Center
專門用來找最大子迴文  
動態規劃法能加速判斷一個字串是不是迴文，但依舊需要遍歷那些已知子字串不是迴文的字串  
中心擴展的核心思想是利用迴文鏡像特性排除非迴文的字串
> [!NOTE]
> 需要注意長度為奇數/偶數的字串需要分別處理

```C#
    public string LongestPalindrome(string s)
    {
        if (string.IsNullOrEmpty(s)) return "";

        int start = 0, maxLen = 0;

        for (int i = 0; i < s.Length; i++)
        {
            ExpandAroundCenter(s, i, i, ref start, ref maxLen);
            ExpandAroundCenter(s, i, i + 1, ref start, ref maxLen);
        }

        return s.Substring(start, maxLen);
    }

    private void ExpandAroundCenter(string s, int left, int right, ref int start, ref int maxLen)
    {
        while (left >= 0 && right < s.Length && s[left] == s[right])
        {
            left--;
            right++;
        }

        int len = right - left - 1;
        if (len > maxLen)
        {
            start = left + 1;
            maxLen = len;
        }
    }
```

# [Medium] Longest Palindromic Substring
https://leetcode.com/problems/longest-palindromic-substring/description/
> [!NOTE]
> [Manacher演算法](https://en.wikipedia.org/wiki/Longest_palindromic_substring#Manacher's_algorithm)  
> 在中心擴展法之上更有效地的剃除不可能的子字串  
> 利用鏡像可以知道子迴文的最大長度，因此可以直接排除比當前最長迴文還要短的迴文  
> 舉個例子: abacaba 是一個迴文，可以發現其中的子迴文 aba 在鏡像位置同樣也是 aba  
> 因此在第 5 個位置時可以馬上得知其最大的迴文長度至少是 3  
> 另外為了統一處理，需要讓字串長度保證為奇數 (透過插入特殊字元)
