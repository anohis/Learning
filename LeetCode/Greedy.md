# [Medium] Container With Most Water
https://leetcode.com/problems/container-with-most-water/description/
> [!NOTE]
> 暴力解會需要 $O(n^2)$，因此需要快速剃除不可能的組合  
> 可以觀察到容量計算取受限於最矮的那邊，因此如果下一個組合最矮的一邊不變，整體容量不可能變得更多  

```C#
    public int MaxArea(int[] height)
    {
        var left = 0;
        var right = height.Length - 1;
        var max = int.MinValue;

        while (left < right)
        {
            var h = height[left] > height[right] ? height[right] : height[left];
            var area = h * (right - left);
            if (area > max)
            {
                max = area;
            }

            if (height[left] < height[right])
            {
                left++;
            }
            else
            {
                right--;
            }
        }

        return max;
    }
```
