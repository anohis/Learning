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
