# Longest Substring Without Repeating Characters
https://leetcode.com/problems/longest-substring-without-repeating-characters/description/
> [!NOTE]
> 首先，想要知道一個不重複字母的子字串長度，可以用開頭和結尾的索引相減   
> 檢查是否有重複字母可以用 hash table  
> 關鍵是檢查到字母重複時如何快速過濾不符合的子字串  
> 當我在第7個字母發現和第3個字母重複時，顯然不必再考慮第3之前的子字串  
> 因此可以直接從第4個開始算

```C#
    public int LengthOfLongestSubstring(string s)
    {
        var maxLen = 0;

        var start = 0;
        var dic = new Dictionary<char, int>();
        for (int i = 0; i < s.Length; i++)
        {
            if (dic.TryGetValue(s[i], out var index))
            {
                start = Math.Max(start, index + 1);
            }

            dic[s[i]] = i;

            var currentLen = i - start + 1;
            if (currentLen > maxLen)
            {
                maxLen = currentLen;
            }
        }

        return maxLen;
    }
```
