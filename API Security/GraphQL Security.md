# GraphQL Security

GraphQL APIs expose a single endpoint (often `/graphql`) with a schema describing all queries and mutations. Misconfigs lead to data leaks and auth bypass.

## Common vulnerabilities

- **Introspection enabled** - full schema visible to attackers
- **BOLA** - access other users' data via ID arguments in queries
- **Batching attacks** - bypass rate limits by bundling requests
- **Alias-based brute force** - multiple login attempts in one HTTP request
- **Deep recursion DoS** - nested queries crash the server

## Testing steps

```bash
# Check introspection
curl -X POST https://target.com/graphql \
  -H "Content-Type: application/json" \
  -d '{"query":"{ __schema { types { name } } }"}'

# Fingerprint engine
graphw00f -t https://target.com/graphql
```

## Tools

| Tool | Use |
|------|-----|
| [InQL](https://github.com/doyensec/inql) | Burp extension for GraphQL |
| [GraphQLmap](https://github.com/swisskyrepo/GraphQLmap) | Automated interaction |
| [BatchQL](https://github.com/assetnote/batchql) | Batching and alias attacks |
| [graphql-cop](https://github.com/nicholasaleks/graphql-cop) | Security audit |

## Practice

- [DVGA - Damn Vulnerable GraphQL Application](https://github.com/dolevf/Damn-Vulnerable-GraphQL-Application)
- PortSwigger Web Security Academy - GraphQL labs

## Related

- [REST API Testing](REST%20API%20Testing.md)
