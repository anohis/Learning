# Tag Helpers
Tag Helpers 是 Razor View 裡可以幫你「擴充 HTML 標籤能力」的語法  
只要加上像 asp-for, asp-action, asp-controller 等屬性，它就會
- 自動產出對應的 HTML
- 幫你跟 model 綁定
- 幫你產生表單、連結、驗證提示等

例如
```CSHTML
<label asp-for="Movie.Title"></label>
```
會自動生成
```HTML
<label for="Movie_Title">Title</label>
```

# 啟用 Tag Helpers
在 view 的 cshtml 中可以加上
```CSHTML
@addTagHelper *, Microsoft.AspNetCore.Mvc.TagHelpers
```
表示加入 Microsoft.AspNetCore.Mvc.TagHelpers 之下的所有 Tag Helpers    
@addTagHelper 的語法如下  
```CSHTML
@addTagHelper *, <某命名空間> 
```
表示也可以使用自訂或三方的 Tag Helpers

> [!NOTE]
> 如果不希望每個檔案都加上這段指令，可以在 Views/_ViewImports.cshtml 添加  
> 此後所有在 Views 底下的 cshtml 都能使用
