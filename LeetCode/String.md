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

# [Medium] String to Integer (atoi)
https://leetcode.com/problems/string-to-integer-atoi/description/

```C#
public int MyAtoi(string s)
{
    s = s.Trim();
    if (string.IsNullOrEmpty(s))
    {
        return 0;
    }

    int i = 0, num = 0, sign = 1;

    if (s[0] == '+' || s[0] == '-')
    {
        sign = (s[0] == '-') ? -1 : 1;
        i++;
    }

    while (i < s.Length && Char.IsDigit(s[i]))
    {
        int digit = s[i] - '0';
        if (num > (Int32.MaxValue - digit) / 10)
        {
            return (sign == 1) ? Int32.MaxValue : Int32.MinValue;
        }
        num = num * 10 + digit;
        i++;
    }

    return sign * num;
}
```
