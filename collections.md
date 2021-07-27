![image](https://user-images.githubusercontent.com/28957748/127150336-10f06920-617d-4f29-9625-e5e457113b78.png)

## List

#### AddRange
```csharp
List<int> a = new List<int>();
a.Add(1);
a.Add(2);

int[] b = new int[3];
b[0] = 3;
b[1] = 4;

a.AddRange(b);
// a now: [1, 2, 3, 4]
```
