# [Medium] 3Sum
https://leetcode.com/problems/3sum/description/
> [!Note]
> 觀察到題目要求總和指定為0，因此可以將問題視為2Sum  
> 但跟 [Two Sum](https://leetcode.com/problems/3sum/description/) 不同的是不保證數字不會重複，另外題目要求組合不重複  
> 所以可以利用排序跳過相同的數字  
> 且陣列排序後又可以使用 Two Pointers 加速搜尋組合
