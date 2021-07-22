# Up and running

## dotnet cli
* Create new solution file
```console
dotnet new sln
```
* Generate api template and output to API/ folder
```console
dotnet new webapi -o API
```
* Add __API__ to the solution
```console
dotnet sln add API
```
* Run api
```console
cd API
dotnet run
dotnet watch run
```
* Trust https
```console
dotnet dev-certs https --trust
```

* Create gitignore file
```console
dotnet new gitignore
```

## Important files
* Program.cs is where the application starts.
* Startup.cs is where lot of the configuration happens.

## VSCode
#### Extensions
* C# for Visual Studio Code (powered by OmniSharp).
* C# Extensions (JosKreativ)
* NuGet Gallery

#### Debug
* `Ctrl + Shift + D`
* `.NET Core Attach`
* `Start`
* `API.exe`

