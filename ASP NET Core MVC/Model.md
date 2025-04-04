# Entity Framework (EF) Core
EF Core 是一種物件關聯式對應 (Object-Relational Mapping, ORM) 架構  
允許使用 C# 類別（Entity）來表示資料表，而不需要直接寫 SQL 查詢

||Entity Framework|ADO.NET (手寫 SQL)|  
|----|----|----|  
|開發速度|高，使用 LINQ 撰寫查詢|低，需要手寫 SQL|
|效能|快，但有 ORM 開銷|更快，因為直接執行 SQL|
|靈活性|可自動生成 SQL，適合快速開發|SQL 最靈活，可調優|
|學習曲線|容易，使用 C# 操作資料|需學 SQL，控制細節|
|維護性|高，物件導向管理資料|低，SQL 需要手動維護|

# POCO (Plain Old CLR Objects)
指的是沒有繼承特定框架或類別庫的純 C# 類別  
目的是讓物件保持簡單、純粹，不受外部框架影響   
例如  
```C#
// not POCO! 
public class Person : IDisposable
{
    private static readonly ILogger Logger = LoggerFactory.Create(typeof(Person));
    public Person() {}
    public Person(int id, string firstName, string lastName, DateTime birthDate)
    {
        Id = id;
        FirstName = firstName;
        LastName = lastName;
        BirthDate = birthDate;
    }
    
    [Category("Data")]
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTime BirthDate { get; set; }

    public string GetName()
    {
        return $"{FirstName} {LastName}";
    }

    public override string ToString() 
        => $"Id:{Id}, FirstName:{FirstName}, LastName:{LastName}, BirthDate:{BirthDate}";
        
    public void Dispose() {/* Code...*/}
}

// POCO
public class Person
{
    public int Id { get; set; }
    public string FirstName { get; set; }
    public string LastName { get; set; }
    public DateTime BirthDate { get; set; }
}
```

POCO 有以下好處
- 不依賴任何框架，因此輕量且容易測試
- 因為輕量，可以簡化序列化和跨層傳遞資料
- 常用於 ORM，POCO 可以當作資料模型，與資料庫物件對應，但不會依賴 EF Core 的特定類別

# Scaffold
Scaffold（樣板生成） 是 Visual Studio 和 .NET CLI（Command Line Interface）提供的一個自動化工具，可以根據現有的資料模型（Model）自動產生控制器（Controller）、視圖（View）和資料存取邏輯（Data Access Code）  
這讓開發者能快速建立 CRUD（Create、Read、Update、Delete）功能，而不需要手寫大量重複的程式碼  

