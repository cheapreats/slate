---
title: CheaprEats Developer Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - shell
  - javascript

toc_footers:
  - <a href='#'>Sign Up for a Developer Key</a>
  - <a href='https://github.com/lord/slate'>Documentation Powered by Slate</a>

includes:
  - types

search: true
---

# Introduction

Welcome to CheaprEats! In this document, you will find all necessary information to get your integration up running with CheaprEats API.

```shell
curl --version
```

```javascript
npm install @cheapreats/sdk
```

We use GraphQL as our API protocol, therefore you can use any GraphQL client you wish, however we do officially have a JavaScript SDK.

# Authentication

> To authorize, use this code:

```shell
# With shell, you can just pass the correct header with each request
curl "https://graphql-v1.cheapreats.com/graphql"
  -H "Authorization: your-token-here"
```

```javascript
const CE = require('@cheapreats/sdk');

CE.setAuthenticationToken('your-token-here');
```

Most endpoints require authorization, there are 3 types of tokens:

* Master token - CheaprEats internal, you probably can't use this.
* Vendor token - Authorizes the call with an employee account for a specific vendor.
* Customer token - Authorizes the call with a customer account.

To authorize, pass the token and the token only in `Authorization` header.

# Groups

## Query Groups

```shell
curl 'https://graphql-v1.cheapreats.com/graphql' \
  -H 'Accept-Encoding: gzip, deflate, br' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Connection: keep-alive' \
  -H 'DNT: 1' \
  -H 'Origin: https://graphql-v1.cheapreats.com' \
  -H 'Authorization: your-token' \
  --data-binary '{"query":"query($select: SelectInput) {\n  groups(select: $select) {\n    _id\n    name\n    customers {\n      _id\n    }\n  }\n}"}' \
  --compressed
```

```javascript
CE.Graph.query(`
  query($select: SelectInput) {
    groups(select: $select) {
      _id
      name
      customers {
        _id
      }
    }
  }
`, {})
  .then(data => {
    console.log(data);
  });
```

> The above command returns JSON structured like this:

```json
{
  "data": {
    "groups": [
      {
        "_id": "507f191e810c19729de860ea",
        "name": "My Group",
        "customers": [
          {
            "_id": "507f1f77bcf86cd799439011"
          }
        ]
      }
    ]
  }
}
```

This endpoint queries all customer groups. Groups are used to logically group customers together, and can be used to A/B testing, targeted notifications etc.

### Query

groups(select: SelectInput): [[Group](#group)]

### Query Parameters

Parameter | Default | Required | Description
--------- | ------- | ---------| ------------
select    | null    |   false  | Standard select filter.

### Authorization

Requires master authorization, CheaprEats internal only.

