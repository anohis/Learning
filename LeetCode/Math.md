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
