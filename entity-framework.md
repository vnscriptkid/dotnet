## Packages
* Microsoft.EntityFrameworkCore
* Microsoft.EntityFrameworkCore.Sqlite (db provider for dev)
* Microsoft.EntityFrameworkCore.Design

## dotnet-ef
```console
dotnet tool install --global dotnet-ef --version 5.0.8
```

## Migrations
```console
dotnet ef migrations add InitialCreate -o Data/Migrations
dotnet ef database update
```
