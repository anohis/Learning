# Add Two Numbers
https://leetcode.com/problems/add-two-numbers/description/
> [!NOTE]
> Linked List的遍歷，需要考慮長短不一致的問題

```C#
    public ListNode AddTwoNumbers(ListNode l1, ListNode l2)
    {
        var newList = new ListNode();

        var last = newList;
        var added = 0;
        while (l1 != null && l2 != null) 
        {
            var sum = l1.val + l2.val + added;
            last.next = new ListNode(sum % 10);
            added = sum / 10;

            l1 = l1.next;
            l2 = l2.next;
            last = last.next;
        }

        while (l1 != null)
        {
            var sum = l1.val + added;
            last.next = new ListNode(sum % 10);
            added = sum / 10;

            l1 = l1.next;
            last = last.next;
        }

        while (l2 != null)
        {
            var sum = l2.val + added;
            last.next = new ListNode(sum % 10);
            added = sum / 10;

            l2 = l2.next;
            last = last.next;
        }

        if (added > 0) 
        {
            last.next = new ListNode(added);
        }

        return newList.next;
    }
```
