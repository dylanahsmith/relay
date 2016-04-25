Relay Input Object Mutations Specification
------------------------------------------

Relay's support for mutations relies on the GraphQL server exposing
mutation fields in a standardized way.

An example of this is the following query:

```graphql
mutation M {
  updateStatus(input: $input) {
    status {
      text
    }
  }
}
```

where the provided parameters are:

```json
{
  "input": {
    "text": "Hello World!"
  }
}
```

and the response is:

```json
{
  "updateStatus": {
    "status": {
      "text": "Hello World!"
    }
  }
}
```

This section of the spec describes the formal requirements around mutations.

# Mutation inputs

In particular, all mutations must expose exactly one argument, named `input`.
This argument's type must be a `NON_NULL` wrapper around an `INPUT_OBJECT`.

# Mutation fields

The return type of any mutation field must be an object.

# Introspection

A server that correctly implements the above requirement will accept the
following introspection query, and return a response that contains the
provided response.

```graphql
{
  __schema {
    mutationType {
      fields {
        type {
          kind
        }
        args {
          name
          type {
            kind
            ofType {
              kind
            }
          }
        }
      }
    }
  }
}
```

yields

```json
{
  "__schema": {
    "mutationType": {
      "fields": [
        // May contain many instances of this
        {
          "type": {
            "kind": "OBJECT"
          },
          "args": [
            {
              "name": "input",
              "type": {
                "kind": "NON_NULL",
                "ofType": {
                  "kind": "INPUT_OBJECT"
                }
              }
            }
          ]
        }
      ]
    }
  }
}
```
