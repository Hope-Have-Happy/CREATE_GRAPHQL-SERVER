# CGA output

This is a sketch of what a `create-graphql-app` scaffolding tool could generate.

The idea is that you would use `create-graphql-app my-app` to create the basic setup (boilerplate around running the app), and `server/` directory.

## Creating types

The next step would be to create types. This example is a super simple Twitter clone, with users and tweets.

The user would run a scaffolding command, such as `create-graphql-app create-type < type.graphql` using a input type description, written the Schema Language and decorated with a set of custom directives.

So this app may have been generated with:

```bash
create-graphql-app create-type < user.graphql
```

Where `user.graphql` looked like:

```graphql
type User {
  username String!
  bio String

  tweets [Tweet!] @hasMany(as: "author")
  liked [Tweet!] @hasAndBelongsToMany(owned: true)

  following [User!] @hasAndBelongsToMany(owned: true)
  followers [User!] @hasAndBelongsToMany(owned: false, as: "following")
}
```

Followed by:

```bash
create-graphql-app create-type < tweet.graphql
```

Where `tweet.graphql` looked like:

```graphql
type Tweet {
  author User!
  body String!

  likers [User!] @hasAndBelongsToMany(owned: false, as: "liked")
}
```

## Created files:

### Schema files

Schema files (such as `schema/user/user.graphql`) are generated by expanding the types above (for instance adding timestamps and ids), and adding CRUD fields to the root `Query`, `Mutation` and `Subscription` types.

Also an input type is created for inserting and modifying documents.

### Resolver files

The resolvers (such as `schema/user/index.js` for the fields added are pretty straightforward and are thing wrappers around the models

### Model files

We also generate model classes for each type (such as `models/User.js`).

Ideally we would use an established library to build the relation functions but I'm not sure there's much out there so at this point we're leaning toward just building each relation (such as `user.following()`) by "hand".
