## Deferred Execution
* Do not run query until you explicitly tell it to run
* How: By chaining as much query as you want and when you're ready

## Pagination info
```json
GET /api/users?pageSize=3&pageNumber=1 HTTP/1.1
```

```json
HTTP/1.1 200 OK
Connection: close
Date: Tue, 27 Jul 2021 13:38:50 GMT
Content-Type: application/json; charset=utf-8
Server: Kestrel
Transfer-Encoding: chunked
Access-Control-Expose-Headers: Pagination
Pagination: {"CurrentPage":1,"ItemsPerPage":3,"TotalItems":14,"TotalPages":5}
```

## HowTo: Paginate
```csharp
int count = await source.CountAsync();
var items = await source.Skip((pageNumber - 1) * pageSize).Take(pageSize).ToListAsync();
```

## HowTo: Attach pagination info to header
```csharp
var paginationHeader = new PaginationHeader(currentPage, itemsPerPage, totalItems, totalPages);

response.Headers.Add("Pagination", JsonSerializer.Serialize(paginationHeader));

response.Headers.Add("Access-Control-Expose-Headers", "Pagination");
```
