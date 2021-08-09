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
* Add new classlib project
```console
dotnet new classlib -n Application
```
* Add reference to classlib
```console
dotnet add reference ../Application
```
* Create gitignore file
```console
dotnet new gitignore
```
* I'm done with adding refs
```console
dotnet restore
```

## Important files
* Program.cs is where the application starts.
* Startup.cs is where lot of the configuration happens.
  * Register app's services (`ConfigureServices`)
  * Create request processing pipeline (`Configure`)

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

