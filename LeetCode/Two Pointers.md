# [Medium] 3Sum
https://leetcode.com/problems/3sum/description/
> [!Note]
> 觀察到題目要求總和指定為0，因此可以將問題視為2Sum  
> 但跟 [Two Sum](https://leetcode.com/problems/3sum/description/) 不同的是必須找到所有可能  
> 如果使用相同方式勢必需要遍歷全部  
> 所以可以排序後使用 Two Pointers 加速搜尋組合
