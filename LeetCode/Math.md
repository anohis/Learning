# [Medium] Reverse Integer
https://leetcode.com/problems/reverse-integer/description/

```C#
    public int Reverse(int x)
    {
        if (x == int.MinValue)
        {
            return 0;
        }

        var sign = Math.Sign(x);
        x = Math.Abs(x);

        var maxValueDig = int.MaxValue % 10;
        var maxValueOther = int.MaxValue / 10;

        var newX = 0;
        while (x > 0)
        {
            if (newX > maxValueOther
            || (newX == maxValueOther && x % 10 > maxValueDig))
            {
                return 0;
            }

            newX = newX * 10 + x % 10;
            x = x / 10;
        }

        return sign * newX;
    }
```

# [Easy] Palindrome Number
https://leetcode.com/problems/palindrome-number/

```C#
    public bool IsPalindrome(int x)
    {
        var str = x.ToString();
        var head = 0;
        var end = str.Length - 1;

        while (head < end)
        {
            if (str[head] != str[end])
            {
                return false;
            }

            head++;
            end--;
        }

        return true;
    }
```
