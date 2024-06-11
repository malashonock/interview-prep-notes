---
tags: [Security]
title: 'Security: Cross-site request forgery (XSRF/CSRF)'
created: '2024-03-28T08:21:17.024Z'
modified: '2024-03-28T08:21:44.508Z'
---

# Security: Cross-site request forgery (XSRF/CSRF)

[SNYK lesson](https://learn.snyk.io/lesson/csrf-attack/)

XSRF is a user's execution of an authorized request that is harmful to the user, without the user's knowing that they even execute such request.

Sequence of actions:
1. The user authenticates with a trusted web app
2. On another tab, the user follows a phishing link that leads to a malicious website
3. The malicious website contains a (usually invisible) form that immediately submits a harmful request to the trusted web app, with the user's credentials (e.g. cookies) appended by the user's browser automatically

**Example**: a banking app that relies solely on session cookies to authenticate users:
1. The user logs in their banking app (e.g. at `https://trusted-bank.com`)
2. The user opens an email and clicks a link that opens a malicious website
3. The malicious website contains code like that:
```
<h1>You've just won $1M!!!</h1>
<form
  action="https://trusted-bank.com/transfer"
  method="POST"
>
  <input
    type="hidden"
    accountNo="1234567890"
    transferAmount="100"
  />
</form>
<script>
  document.forms[0].submit();
</script>
```
4. The above code executes immediately, which produces an HTTP request as follows:
```
POST /transfer HTTP/1.1
Host: trusted-bank.com
Content-Type: application/x-www-form-urlencoded
Cookie: session=[userAuthenticationCookie]
accountNo=1234567890&amount=100
```

**Common source of vulnerability**:
- trusted app relying on session cookies authentication
- cookies are set without `SameSite` attribute
- predictable request parameters

**Threat mitigation**:
- **CSRF tokens**: a unique, unpredictable, shared secret between the server and a trusted client
  - the server sets a cookies with an CSRF token
  - the trusted client appends CRSF token as a custom header to every request
- **`SameSite`** attribute set on cookies:
  - `Strict`: the browser will not append the cookie to any requests that originate from another site, e.g.:
    - `trusted-bank.com` sets a cookie with `SameSite=Strict`
    - if the user's browser sends a request to `trusted-bank.com` from a tab opened at `trusted-bank.com`, it appends the cookie
    - if the user's browser sends a request to `trusted-bank.com` from a tab opened at `malicious-site.com`, it will NOT append the cookie
  - `Lax`: same as `Strict`, plus:
    - the request method is `GET`
    - the request is initiated by the user's action, not script 
