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

# [Medium] Integer to Roman
https://leetcode.com/problems/integer-to-roman/description/

```C#
    public string IntToRoman(int num)
    {
        var str = new StringBuilder();
        WriteStr(str, ref num, 1000, "M");
        WriteStr(str, ref num, 900, "CM");
        WriteStr(str, ref num, 500, "D");
        WriteStr(str, ref num, 400, "CD");
        WriteStr(str, ref num, 100, "C");
        WriteStr(str, ref num, 90, "XC");
        WriteStr(str, ref num, 50, "L");
        WriteStr(str, ref num, 40, "XL");
        WriteStr(str, ref num, 10, "X");
        WriteStr(str, ref num, 9, "IX");
        WriteStr(str, ref num, 5, "V");
        WriteStr(str, ref num, 4, "IV");
        WriteStr(str, ref num, 1, "I");
        return str.ToString();
    }

    private void WriteStr(StringBuilder str, ref int num, int value, string token)
    {
        while (num >= value)
        {
            num -= value;
            str.Append(token);
        }
    }
```
