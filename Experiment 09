# Experiment 9

## Cookie security using Wireshark and Cookie-Editor

## Aim

To inspect a test session's cookie attributes, compare HTTP and HTTPS traffic, and observe how HttpOnly changes JavaScript access to cookies.

## Theory

HTTP requests are independent messages. A web application can maintain a session by issuing an identifier that the browser returns in a cookie when its domain, path, security and other matching conditions permit. The server associates that identifier with session state. Possession of a valid session token may allow impersonation, depending on the application's other checks, making session tokens sensitive credentials.

Plain HTTP can expose cookie headers to someone able to observe the traffic. TLS protects HTTP application data in transit. A normal capture without TLS session keys should show encrypted application data for HTTPS, rather than readable cookie values. An empty `http.cookie` result alone is insufficient proof of protection because the wrong interface, missing traffic or protocol-decoding differences can also produce no matches.

| Attribute or control | Security role |
| --- | --- |
| `Secure` | Restricts cookie transmission to secure connections |
| `HttpOnly` | Excludes the cookie from the JavaScript cookie API |
| `SameSite=Lax` / `Strict` | Restricts sending in cross-site contexts according to the selected policy |
| `SameSite=None` | Permits cross-site use and requires `Secure` in supporting browsers |
| Domain and Path | Determine where a cookie may be sent |
| Expiry and server-side lifetime | Limit persistence and session validity |

HttpOnly reduces script-based token extraction, but an injected script may still perform authenticated actions through the browser. SameSite helps control cross-site request risks and should be combined with appropriate CSRF protection. Server-side expiry and invalidation matter even when a browser displays a later cookie expiry date. The attribute definitions and JavaScript-access distinction are documented in the [MDN Set-Cookie reference](https://developer.mozilla.org/en-US/docs/Web/HTTP/Reference/Headers/Set-Cookie).

## Requirements

Wireshark, a browser, Cookie-Editor and an account or local application designated for the exercise. Record cookie names and flags without publishing live token values.

## Commands and snippets extracted from the PDF

### Install Wireshark — PDF page 12

```bash
sudo apt update
sudo apt install wireshark -y
```

### Wireshark display filters — PDF page 14

Use each filter separately in the display-filter bar.

```text
http.cookie
tls
```

### Inspect JavaScript-visible cookies — PDF page 18

Run this expression in the browser console on the test application's own origin.

```javascript
document.cookie
```

### Cookie-header examples — PDF pages 7 and 19

These are explanatory HTTP header examples. The ellipsis and sample values are placeholders, not live credentials.

```http
Set-Cookie: xs=…; Secure; HttpOnly; SameSite=Lax
Cookie: login=test; sessionid=abc123
```

## Procedure

1. Open the assigned test site and start capture on the interface carrying its traffic.
2. Sign in using only the designated test account and generate an authenticated request.
3. Apply `http.cookie` and `tls`. Confirm that the capture contains the expected connection before interpreting an empty filter.
4. In Cookie-Editor, record the cookie name, domain, path, Secure, HttpOnly, SameSite and expiry fields. Mask values in screenshots.
5. Compare the browser-console expression with the cookie list. HttpOnly cookies should be absent from `document.cookie` even though the browser stores them.
6. If a designated HTTP test application is available, compare its readable headers with the HTTPS capture using disposable test data.
7. Log out and examine session invalidation in the test application.

The PDF illustrates Facebook's `xs` cookie. Its name, values and attributes are examples from that demonstration, rather than fixed requirements for every application. The repository's screenshot record also includes terminal captures from a keylogging demonstration. This theory file follows the supplied Cookie Security handout for Experiment 9, while the dedicated keylogging theory belongs to Experiment 11.

## Observations, prevention and result

Record which traffic was actually captured, whether a cookie header was readable, and which flags were observed. HTTPS with Secure protects transport, HttpOnly restricts script access, and SameSite constrains cross-site sending. Session rotation, short server-side lifetimes, logout invalidation, contextual output encoding and CSRF controls strengthen the application. The exercise demonstrates these controls through inspection rather than reuse of another person's session.

## Viva questions

- Why can Cookie-Editor display a token that Wireshark cannot read in TLS traffic?
- Why does an HttpOnly cookie disappear from `document.cookie`?
- How do cookie expiry and server-side session validity differ?

## Sources

`Cookie_Security_Experiment.pdf`, PDF pages 2–26. Existing practical evidence is in [Experiment 9.pdf](<Experiment 9.pdf>).
