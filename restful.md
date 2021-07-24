## 🆕 201 Created
* Header of response must contain url to retrieve created content
```json
HTTP/1.1 201 Created
Connection: close
Date: Sat, 24 Jul 2021 06:39:46 GMT
Content-Type: application/json; charset=utf-8
Server: Kestrel
Transfer-Encoding: chunked
Location: https://localhost:5001/api/Users/ashley

{
  "id": 16,
  "url": "https://res.cloudinary.com/vnscriptkid/image/upload/v1627108786/kgmurlbjel8iv4bue7xq.jpg",
  "isMain": false
}
```
* Code
```csharp
[HttpGet("{username}", Name = "GetUser")]
public async Task<ActionResult<MemberDto>> GetUser(string username)
{
  // ...
}

public async Task<ActionResult<PhotoDto>> AddPhoto(IFormFile file)
{
  // ...
  return CreatedAtRoute("GetUser", new { username = user.Username }, _mapper.Map<PhotoDto>(photo));
```
