# What is Prototype Pollution?

Prototype pollution is a JavaScript-specific bug where an attacker injects properties into `Object.prototype`, the base object that almost every other object inherits from. Pollute the prototype and you change the default behavior of objects across the whole application - which can lead to denial of service, property tampering, XSS, or even remote code execution in Node.js.

## How it works

In JavaScript, objects inherit from a prototype. Keys like `__proto__`, `constructor`, and `prototype` reach that shared parent. If code merges user input into an object unsafely (deep merge, `Object.assign` on nested data, query-string parsers), an attacker can walk up to the prototype:

```javascript
// VULNERABLE deep merge
merge({}, JSON.parse(userInput));
```

```json
{ "__proto__": { "isAdmin": true } }
```

Now *every* object in the app has `isAdmin === true` unless it sets its own value.

## Client-side vs server-side

- **Client-side (browser):** pollute via URL params or JSON, then look for a "gadget" that turns a polluted property into [XSS](XSS.md) (e.g. a library that reads an undefined option from the prototype).
- **Server-side (Node.js):** pollute via a JSON body or query string, then chain to a gadget that causes RCE (template engines, child_process options) or auth bypass.

## Test payloads

```text
# Query string / body
?__proto__[isAdmin]=true
?constructor[prototype][isAdmin]=true

# JSON body
{"__proto__": {"polluted": "yes"}}
{"constructor": {"prototype": {"polluted": "yes"}}}
```

Confirm in a browser console:

```javascript
({}).polluted        // if it returns "yes", the prototype is polluted
```

## Tools

- [Burp Suite](../../Tools/Burp%20Suite.md) + the **DOM Invader** tool (auto-detects client-side prototype pollution and gadgets)
- Node.js gadget research / `ppfuzz` for server-side

## Mitigation

- Reject or strip `__proto__`, `constructor`, and `prototype` keys in user input.
- Use `Object.create(null)` for map-like objects (no prototype to pollute).
- Use `Map` instead of plain objects for key/value data.
- Use safe merge libraries and `Object.freeze(Object.prototype)` where feasible; validate with a schema.

## CWE

- CWE-1321: Improperly Controlled Modification of Object Prototype Attributes

## Deep dive

- [PortSwigger prototype pollution labs](https://portswigger.net/web-security/prototype-pollution)
