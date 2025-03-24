# Two Sum
https://leetcode.com/problems/two-sum/description/  

> [!NOTE]
> 題目保證只有一個解且不會有相同數字，這表示每個元素匹配的數字絕對不一樣  
> 因此可以使用 Dictionary 儲存訪問過的元素

```C#
    public int[] TwoSum(int[] nums, int target)
    {
        var dic = new Dictionary<int, int>();
        
        for(int i = 0; i < nums.Length; i++)
        {
            var remain = target - nums[i];
            if(dic.TryGetValue(remain, out var index))
            {
                return new int[] { index, i };
            }
            dic[nums[i]] = i;
        }

        return new int[] { -1, -1 };
    }
```
