# Dynamic Programming
適用範圍
- 最優子結構 (Optimal substructure): 如果可以從某個問題的子問題的最優解建構出一個最優解，則稱該問題具有最優子結構
  - 遞迴 
- 重疊子問題 (Overlapping Subproblems): 如果問題可以分解為多次重複使用的子問題，則稱該問題具有重疊子問題
  - 記憶化 (Memoization)

# [Medium] Longest Palindromic Substring
https://leetcode.com/problems/longest-palindromic-substring/description/
> [!NOTE]
> 關鍵在於如何快速剃除不可能的子字串  
> 可以利用迴文有一個特性: 鏡像  
> 鏡像後的字母相同 -> 鏡像後的子迴文相同  
> 舉個例子: abacaba 是一個迴文，可以發現其中的子迴文 aba 在鏡像位置同樣也是 aba  
> 因此在第 5 個位置時可以馬上得知其最大的迴文數量是 3

> [!NOTE]
> [Manacher演算法](https://en.wikipedia.org/wiki/Longest_palindromic_substring#Manacher's_algorithm)  
> 關鍵點
> - 為了保證字母一定有鏡像位置，需要讓字串長度保證為奇數
> - 透過鏡像快速計算子迴文最大長度

```C#
    public string LongestPalindrome(string s)
    {
        var str = "#" + string.Join("#", s.ToCharArray()) + "#";

        var maxRadiusArr = new int[str.Length];
        var prevCenter = 0;
        var prevRadius = 0;
        for (int center = 0; center < str.Length; center++)
        {
            var radius = 0;

            if (center < prevCenter + prevRadius)
            {
                var mirrorCenter = prevCenter - (center - prevCenter);
                if (mirrorCenter >= 0)
                {
                    var mirrorRadius = maxRadiusArr[mirrorCenter];

                    if (center + mirrorRadius < prevCenter + prevRadius)
                    {
                        maxRadiusArr[center] = mirrorRadius;
                        continue;
                    }
                    else
                    {
                        radius = prevCenter + prevRadius - center;
                    }
                }
            }

            var left = center - radius;
            var right = center + radius;
            while (left >= 0
                && right < str.Length
                && str[left] == str[right])
            {
                radius++;
                left = center - radius;
                right = center + radius;
            }

            maxRadiusArr[center] = radius;
            prevCenter = center;
            prevRadius = radius;
        }

        var maxCenter = 0;
        var maxRadius = int.MinValue;
        for (int i = 0; i < maxRadiusArr.Length; i++)
        {
            if (maxRadiusArr[i] > maxRadius)
            {
                maxCenter = i;
                maxRadius = maxRadiusArr[i];
            }
        }

        return str.Substring(maxCenter - maxRadius + 1, 2 * maxRadius - 1)
            .Replace("#", "");
    }
```

