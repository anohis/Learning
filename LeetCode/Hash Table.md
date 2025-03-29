# Two Sum
https://leetcode.com/problems/two-sum/description/  

> [!NOTE]
> Two Sum 是 K Sum 的一個特例  
> K Sum 一般使用 Two Pointers 解  
> 但在 Two Sum 則有另一個方案: Hash Table    
> 在 Two Sum 中，Two Pointers 和 Hash Table 都是 O(N)  
> 但 Two Pointers 的先決條件是排序，需要額外花費 O(NlogN)  
> 因此在不保證排序的情況下使用 Hash Table 反而更快
