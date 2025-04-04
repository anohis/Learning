# Entity Framework (EF) Core
EF Core 是一種物件關聯式對應 (Object-Relational Mapping, ORM) 架構  
允許使用 C# 類別（Entity）來表示資料表，而不需要直接寫 SQL 查詢

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
