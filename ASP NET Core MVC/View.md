# View
View 的副檔名是 .cshtml，且名稱必須和 action 命名一致

# Layout
檢查 Views/_ViewStart.cshtml 檔案

```C#
@{
    Layout = "_Layout";
}
```

Views/_ViewStart.cshtml 檔案會將 Views/Shared/_Layout.cshtml 檔案帶入每個 View

# ViewData
使用 ViewData 可以在 View 和 Controller 間傳遞資料  

HelloWorldController.cs
```C#
public class HelloWorldController : Controller
{
    public IActionResult Index()
    {
        return View();
    }
    public IActionResult Welcome(string name, int numTimes = 1)
    {
        ViewData["Message"] = "Hello " + name;
        ViewData["NumTimes"] = numTimes;
        return View();
    }
}
```

Welcome.cshtml
```C#
@{
    ViewData["Title"] = "Welcome";
}

<h2>Welcome</h2>

<ul>
    @for (int i = 0; i < (int)ViewData["NumTimes"]!; i++)
    {
        <li>@ViewData["Message"]</li>
    }
</ul>
```



