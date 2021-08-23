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
  - Store in cookie
- Jwt
  - Token that holds user's data itself => value token
  - Store in localStorage

## Jwt structure
- 3 parts
  - Header: Meta info, algorithms
  - Payload: User info like userId, email
  - Signature: Verify if `Header` && `Payload` is correctly provided

## Signing
https://auth0.com/docs/tokens/signing-algorithms
- Either of 2
  - RS256 (RSA Signature with SHA-256): asymmetric 
  - HS256 (HMAC with SHA-256): symmetric 

## Verify
<img height="200" src="https://user-images.githubusercontent.com/28957748/128695951-ff972282-3e37-4cf6-a594-42d755c2cf16.png" />

## Best Practice
https://blog.ropnop.com/storing-tokens-in-browser/
- No secret user's info in Payload
- Keep token yourself
- Disable token: blacklist
- jwt token 
  - should be short-lived, 
  - stored in localStorage (accross domains, persist between tabs, after closing browsers)
- refresh-token 
  - is long-lived
  - avoid re-login when jwt expires
  - stored in cookie (js can't manipulate, sent with every reqs, not accross domains, persist)
  - hackers can use refreshToken to exchange for token? Refresh token rotation: every time an application exchanges a refresh token to get a new access token, a new refresh token is also returned
