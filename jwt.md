## What
* __Authorize__ users (previously logged in) not authenticate

## Session-based:
- How : 
  - Client: Save session_id in cookie, send along with every req
  - Server: Stores mapping between session_id and user's info
- Cons:
  - Multiple servers behind a load balancer
    - Solution 1: There must be a shared cache storing session data => one point of failure
    - Solution 2: Load balancer remembers which server stores session of a specific user
  - 👉 Hard to scale


## Jwt vs Session
- Session-based
  - Token that has reference to user's data => reference token
- Jwt
  - Token that holds user's data itself => value token
