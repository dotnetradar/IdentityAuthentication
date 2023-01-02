```c#
//Add Database Context To Program Class And Creating Database By Migration

//Add Below Line Before "var app = builder.Build();"
builder.Services.AddDbContext<EFContext>();

//Open Package Manager Console
//Type "Add-Migration init" Press Enter 
//Type "Update-Database" Press Enter

```
