# [Medium] Zigzag Conversion
https://leetcode.com/problems/zigzag-conversion/description/
> [!NOTE]
> 單純找規律  
> 以 4 列來看，可以發現每一列的間隔分別為
> - 6 > 6 > 6 ...
> - 4 > 2 > 4 ...
> - 2 > 4 > 2 ...
> - 6 > 6 > 6 ...  

```C#
public string Convert(string inputString, int numRows)
{
    if (numRows == 1)
    {
        return inputString;
    }

    var result = new char[inputString.Length];
    var resultIndex = 0;
    var period = numRows * 2 - 2;
    var increment = period;
    for (var strIndex = 0; strIndex < inputString.Length; strIndex += increment)
    {
        result[resultIndex++] = inputString[strIndex];
    }

    for (int row = 1; row < numRows - 1; row++)
    {
        increment = row * 2;
        for (var i = row; i < inputString.Length; i += increment)
        {
            result[resultIndex++] = inputString[i];
            increment = period - increment;
        }
    }

    increment = period;
    for (var strIndex = numRows - 1; strIndex < inputString.Length; strIndex += increment)
    {
        result[resultIndex++] = inputString[strIndex];
    }

    return new string(result);
}
```
