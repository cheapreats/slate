---
title: CheaprEats Developer Documentation

language_tabs: # must be one of https://git.io/vQNgJ
  - graphql
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

```graphql
Authorization: your-token-here
```

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

# Select Filter

Many queries and fields have an optional parameter `select` with `SelectInput` type. This parameter can be used to specify how do you want to filter the returned data.

Inside `SelectInput`, there is a field called `where`, where filters can be used to filter results by matching field values. Similar to `WHERE` clause in SQL.

## Basic Usage

```graphql
{
  vendors(
    select: {
      where: {
        filters: [
          {field: "_id", match: "5b379c2b826ce00a0be2ae68"}
        ]
      }
    }
  ) {
    _id
    name
  }
}
```

```shell
curl 'https://graphql-v1.cheapreats.com/graphql' \
  -H 'Accept-Encoding: gzip, deflate, br' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Connection: keep-alive' \
  -H 'DNT: 1' \
  -H 'Origin: https://graphql-v1.cheapreats.com' \
  -H 'Authorization: your-token' \
  --data-binary '{"query":"{\n  vendors(\n    select: {\n      where: {\n        filters: [\n          {field: \"_id\", match: \"5b379c2b826ce00a0be2ae68\"}\n        ]\n      }\n    }\n  ) {\n    _id\n    name\n  }\n}"}' \
  --compressed
```

```javascript
CE.Graph.query(`
{
  vendors(
    select: {
      where: {
        filters: [
          {field: "_id", match: "5b379c2b826ce00a0be2ae68"}
        ]
      }
    }
  ) {
    _id
    name
  }
}
`);
```

You can use this filter to easily filter by a field.

For example, if you want to filter a list of vendors by `_id`, then you can add a new entry in `filters` array, and specify the field `_id` to match `5b379c2b826ce00a0be2ae68`.


## Field Operators

```graphql
{
  vendors(
    select: {
      where: {
        filters: [
          {field: "name", match: "(t|T)he", operator: REGEX}
        ]
      }
    }
  ) {
    _id
    name
  }
}
```

```shell
curl 'https://graphql-v1.cheapreats.com/graphql' \
  -H 'Accept-Encoding: gzip, deflate, br' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Connection: keep-alive' \
  -H 'DNT: 1' \
  -H 'Origin: https://graphql-v1.cheapreats.com' \
  -H 'Authorization: your-token' \
  --data-binary '{"query":"{\n  vendors(\n    select: {\n      where: {\n        filters: [\n          {field: \"name\", match: \"(t|T)he\", operator: REGEX}\n        ]\n      }\n    }\n  ) {\n    _id\n    name\n  }\n}"}' \
  --compressed
```

```javascript
CE.Graph.query(`
{
  vendors(
    select: {
      where: {
        filters: [
          {field: "name", match: "(t|T)he", operator: REGEX}
        ]
      }
    }
  ) {
    _id
    name
  }
}
`);
```

Above query will match the ID exactly, however there are more operators you can use, such as `REGEX`, by setting the operator as `REGEX` we can construct a query to find all vendors who's name have the word `the`.

## Logical Operators

```graphql
{
  vendors(
    select: {
      where: {
        operator: OR,
        filters: [
          {field: "name", match: "(t|T)he", operator: REGEX},
          {field: "address", match: "Yonge", operator: REGEX}
        ]
      }
    }
  ) {
    _id
    name
    address
  }
}
```

```shell
curl 'https://graphql-v1.cheapreats.com/graphql' \
  -H 'Accept-Encoding: gzip, deflate, br' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Connection: keep-alive' \
  -H 'DNT: 1' \
  -H 'Origin: https://graphql-v1.cheapreats.com' \
  -H 'Authorization: your-token' \
  --data-binary '{"query":"{\n  vendors(\n    select: {\n      where: {\n        operator: OR,\n        filters: [\n          {field: \"name\", match: \"(t|T)he\", operator: REGEX},\n          {field: \"address\", match: \"Yonge\", operator: REGEX}\n        ]\n      }\n    }\n  ) {\n    _id\n    name\n    address\n  }\n}"}' \
  --compressed
```

```javascript
CE.Graph.query(`
{
  vendors(
    select: {
      where: {
        operator: OR,
        filters: [
          {field: "name", match: "(t|T)he", operator: REGEX},
          {field: "address", match: "Yonge", operator: REGEX}
        ]
      }
    }
  ) {
    _id
    name
    address
  }
}
`);
```

By default, when you have multiple filters, `AND` logical operator will be used to connect them, however you can set it to `OR` if you wish. The example query will return all stores that contains the word `the` or is on Yonge street.

## Nested Logical Groups

```graphql
{
  vendors(
    select: {
      where: {
        operator: OR,
        filters: [
          {field: "_id", match: "5d49c1028d692850fcc0a3de", operator: EQUALS}
        ],
        filter_groups: {
          operator: AND,
          filters: [
            {field: "address", match: "Street", operator: REGEX},
            {field: "created_at", match: "2019-10-25T00:00:00.928Z", operator: GREATER_THAN}
          ]
        }
      }
    }
  ) {
    _id
    name
    address
  }
}
```

```shell
curl 'https://graphql-v1.cheapreats.com/graphql' \
  -H 'Accept-Encoding: gzip, deflate, br' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Connection: keep-alive' \
  -H 'DNT: 1' \
  -H 'Origin: https://graphql-v1.cheapreats.com' \
  -H 'Authorization: your-token' \
  --data-binary '{"query":"{\n  vendors(\n    select: {\n      where: {\n        operator: OR,\n        filters: [\n          {field: \"_id\", match: \"5d49c1028d692850fcc0a3de\", operator: EQUALS}\n        ],\n        filter_groups: {\n          operator: AND,\n          filters: [\n            {field: \"address\", match: \"Street\", operator: REGEX},\n            {field: \"created_at\", match: \"2019-10-25T00:00:00.928Z\", operator: GREATER_THAN}\n          ]\n        }\n      }\n    }\n  ) {\n    _id\n    name\n    address\n  }\n}"}' \
  --compressed
```

```javascript
CE.Graph.query(`
{
  vendors(
    select: {
      where: {
        operator: OR,
        filters: [
          {field: "_id", match: "5d49c1028d692850fcc0a3de", operator: EQUALS}
        ],
        filter_groups: {
          operator: AND,
          filters: [
            {field: "address", match: "Street", operator: REGEX},
            {field: "created_at", match: "2019-10-25T00:00:00.928Z", operator: GREATER_THAN}
          ]
        }
      }
    }
  ) {
    _id
    name
    address
  }
}
`);
```

You can even connect multiple groups of filters that uses different logical operators! Simply put a list of groups inside `filter_groups` array. The example query will return the store with ID `5d49c1028d692850fcc0a3de`, plus all stores that resides on a street and is created after October 25th.

## Pagination Operators

```graphql
{
  orders(select:{
    limit:10, skip:1
  }) {
    _id
    updated_at
  }
}
```

```shell
curl 'https://graphql-v1.cheapreats.com/graphql' \
  -H 'Accept-Encoding: gzip, deflate, br' \
  -H 'Content-Type: application/json' \
  -H 'Accept: application/json' \
  -H 'Connection: keep-alive' \
  -H 'DNT: 1' \
  -H 'Origin: https://graphql-v1.cheapreats.com' \
  -H 'Authorization: your-token' \
  --data-binary '{"query":"{\n  orders(\n    select: {\n limit: 10, skip: 1 }\n      }\n    }\n  ) {\n    _id\n    updated_at\n  }\n}"}' \
  --compressed
```

```javascript
CE.Graph.query(`
  {
    orders(select:{
      limit:10, skip:1
    }) {
      _id
      updated_at
    }
  }
`);
```

The select filter allows you to specify two operators, `limit` and `skip`.

* `limit` allows you to limit the number of results returned. By default, the results returned will be 100. The valid values for `limit` are between [1-100].

* `skip` allows you to skip the desired number of starting results. By default, 0 results are skipped. The valid values for `limit` is > 0.



# Groups

## Query Groups

```graphql
query($select: SelectInput) {
  groups(select: $select) {
    _id
    name
    customers {
      _id
    }
  }
}
```

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
