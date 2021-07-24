## Use case 1: Extend functionality of an existing class, avoid boilerplate code
* Desired behavior
```csharp
var dateOfBirth = new DateTime(1995, 09, 21);
dateOfBirth.calculateAge();
```
* Extend functions of `DateTime` class
```csharp
public static class DateTimeExtensions
{
    public static int CalculateAge(this DateTime dob)
    {
        var today = DateTime.Now;
        return today.Year - dob.Year;
    }
}
