## What
- A way to model database table

## Usecases
* Fields exist in db
```csharp
public int SenderId { get; set; }
```
* Virtual fields, exist in code only
```csharp
public AppUser Sender { get; set; }
```
* Default to null
```csharp
public DateTime? DateRead { get; set; }
```
* Specify default value
```csharp
public DateTime MessageSent { get; set; } = DateTime.Now;
```
