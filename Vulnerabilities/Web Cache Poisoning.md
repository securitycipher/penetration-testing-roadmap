# What is Web Cache Poisoning?

Web cache poisoning tricks a caching layer (CDN, reverse proxy, or the app's own cache) into storing a malicious response, which is then served to every other user who requests that page. One request from you, and the poisoned response hits thousands of victims. The related **web cache deception** attack does the reverse: it tricks the cache into storing a victim's private page where you can read it.

## How caching works (and breaks)

A cache decides what to store using a **cache key** - usually the URL and a few headers. Anything *not* in the cache key is "unkeyed." If an unkeyed input changes the response (for example an unkeyed header reflected into the page), you can poison the cached copy for everyone sharing that key.

## Cache poisoning - finding it

1. Find an **unkeyed input** that affects the response (headers like `X-Forwarded-Host`, `X-Forwarded-Scheme`, `X-Host`, or odd query params).
2. Confirm it is reflected or changes behavior.
3. Confirm the response is **cacheable** (look for `Cache-Control`, `X-Cache: hit/miss`, `Age`).
4. Send a payload via the unkeyed input and verify the poisoned response is now served to a normal request.

```http
GET /en HTTP/1.1
Host: victim.tld
X-Forwarded-Host: attacker.tld       # if reflected into a <script src> -> stored XSS for all

GET /?cb=1 HTTP/1.1
X-Forwarded-Scheme: nothttps          # can trigger a cached redirect loop / DoS
```

Watch `X-Cache` headers: `miss` then `hit` on a second request with a clean URL confirms poisoning.

## Web cache deception - finding it

1. Request a private page with a fake static extension appended: `/account/profile.css` or `/account/profile/nonexistent.jpg`.
2. If the app returns the private content *and* the cache stores it as a "static file," fetch the same URL unauthenticated.
3. You now read another user's cached private data.

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) + the **Param Miner** extension (finds unkeyed inputs)
- Watch `Age`, `X-Cache`, `Cache-Control`, `Vary` headers

## Mitigation

- Include every input that affects the response in the **cache key**, or do not cache those responses.
- Do not reflect untrusted headers into responses.
- Cache static files by content type, not just URL extension; disable caching for authenticated pages.
- Set a sensible `Vary` and strict `Cache-Control` on dynamic/private responses.

## CWE

- CWE-444: Inconsistent Interpretation of HTTP Requests
- CWE-525: Use of Web Browser Cache Containing Sensitive Information

## Deep dive

- [PortSwigger web cache poisoning labs](https://portswigger.net/web-security/web-cache-poisoning)
- [PortSwigger web cache deception labs](https://portswigger.net/web-security/web-cache-deception)
