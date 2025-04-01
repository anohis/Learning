# [Medium] Divide Two Integers
https://leetcode.com/problems/divide-two-integers/description/
> [!NOTE]
> 不允許使用除法的情況下只能用減法  
> 但極端情況下如除數為 $1$ 被除數為 $2^{32}-1$ ，所需要的運算量過大  
> 因此使用二進位來計算商數  
> 從最高位開始逐步減去，最多只需要 $32$ 次就能計算出結果
