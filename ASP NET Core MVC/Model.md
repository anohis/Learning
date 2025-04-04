
搭配 Entity Framework Core (EF Core) 使用這些模型類別，即可使用資料庫  
EF Core 是一種物件關聯式對應 (ORM) 架構，可簡化資料存取程式碼

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
