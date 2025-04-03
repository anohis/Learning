# Controllers
Controller 會處理 URL 區段和查詢字串值，並將這些值傳遞至 Model
- `https://localhost:5001/Home/Privacy`：會指定 HomeController.Privacy()
- `https://localhost:5001/Movies/Edit/5`：是使用 Movies 控制器和 Edit 動作來編輯 ID=5 之電影的要求

Controller 中的每個 public 方法可呼叫作為 HTTP 端點(HTTP endpoint)  

> [!NOTE]
> HTTP 端點：
> 是 Web 應用程式中的可鎖定 URL，例如 `https://localhost:5001/HelloWorld`
> 表示：
> - 所用的通訊協定：HTTPS
> - Web 伺服器的網路位置，包括 TCP 埠：localhost:5001
> - 目標 URI：HelloWorld

MVC 使用的預設 URL 路由邏輯會使用像這樣的格式來判斷要叫用的程式碼

$$
/[Controller]/[ActionName]/[Parameters]
$$

> [!NOTE]
> 路由格式可以在 Program.cs 檔案中設定
> ```C#
> app.MapControllerRoute(
>     name: "default",
>     pattern: "{controller=Home}/{action=Index}/{id?}");
> ```
> 格式表明 Index 是在未明確指定 action 名稱時，會在 Controller 上呼叫的預設 action

例如
```C#
public class HelloWorldController : Controller
{
    // GET: /HelloWorld/
    public string Index()
    {
        return "This is my default action...";
    }
    // GET: /HelloWorld/Welcome/ 
    public string Welcome()
    {
        return "This is the Welcome action method...";
    }
    // GET: /HelloWorld/Hello?name=Rick&numtimes=4
    // Requires using System.Text.Encodings.Web;
    public string Hello(string name, int numTimes = 1)
    {
        return HtmlEncoder.Default.Encode($"Hello {name}, NumTimes is: {numTimes}");
    }
}
```
> [!NOTE]
> 因為 action 不能重複，因此不能使用 Welcome 多載

> [!NOTE]
> name 和 numTimes 參數是在查詢字串中傳遞  
> 上述 URL 中的 ? (問號) 是分隔符號，查詢字串（Query String）則緊隨其後  
> & 字元會分隔域值組
