---
outline: deep
---

# Service Schemas and Resolvers

The `<service>.schemas` file contains the [schemas and resolvers](../../api/schema/index.md) for this service.

<BlockQuote type="info">

The examples on this page are using [TypeBox](../../api/schema/typebox.md). For more information on plain JSON schema see the [JSON schema API documentation](../../api/schema/schema.md).

</BlockQuote>

## Patterns

There a four main types of schemas and resolvers. The schemas, resolvers and TypeScript types are declared as follows:

```ts
// The schema definition
export const nameSchema = Type.Object({
  text: Type.String()
})
// The TypeScript type inferred from the schema
export type Name = Static<typeof nameSchema>
// The validator for the schema
export const nameValidator = getValidator(nameSchema, dataValidator)
// The resolver for the schema
export const nameResolver = resolve<Name, HookContext>({
  properties: {}
})
```

## Main Schemas and Resolvers

This schema defines the main data model of all properties and is normally the shape of the data that is returned. This includes database properties as well as associations and other computed properties.

```ts
// Main data model schema https://dove.feathersjs.com/guides/cli/schemas-and-resolvers.html#main-schemas-and-resolvers
export const messagesSchema = Type.Object(
  {
    _id: objectId,
    text: Type.String()
  },
  { $id: 'Messages', additionalProperties: false }
)
export type Messages = Static<typeof messagesSchema>
export const messagesResolver = resolve<Messages, HookContext>({
  properties: {}
})
```

## External Resolvers

```ts
// External resolvers https://dove.feathersjs.com/guides/cli/schemas-and-resolvers.html#external-resolvers
export const messagesExternalResolver = resolve<Messages, HookContext>({
  properties: {}
})
```

## Data Schema and Resolvers

```ts
// Schema for creating new entries https://dove.feathersjs.com/guides/cli/schemas-and-resolvers.html#data-schema-and-resolvers
export const messagesDataSchema = Type.Pick(messagesSchema, ['string'], {
  $id: 'MessagesData',
  additionalProperties: false
})
export type MessagesData = Static<typeof messagesDataSchema>
export const messagesDataValidator = getValidator(messagesDataSchema, dataValidator)
export const messagesDataResolver = resolve<Messages, HookContext>({
  properties: {}
})
```

### create, patch and update

## Query Schema and Resolvers

```ts
// Schema for allowed query properties https://dove.feathersjs.com/guides/cli/schemas-and-resolvers.html#query-schema-and-resolvers
export const messagesQueryProperties = Type.Pick(messagesSchema, ['_id', 'name'], {
  additionalProperties: false
})
export const messagesQuerySchema = querySyntax(messagesQueryProperties)
export type MessagesQuery = Static<typeof messagesQuerySchema>
export const messagesQueryValidator = getValidator(messagesQuerySchema, queryValidator)
export const messagesQueryResolver = resolve<MessagesQuery, HookContext>({
  properties: {}
})
```

### Schemas vs MongoDB Validation

In Feathers v5 (Dove) we added support for Feathers Schema, which performs validation and provides TypeScript types. Recent versions of MongoDB include support for JSON Schema validation at the database server. Most applications will benefit from using Feathers Schema for the following reasons.

- Feathers Schema's TypeBox integration makes JSON Schema so much easier to read and write.
- You get TypeScript types for free once you've defined your validation rules, using `TypeBox` or `json-schema-to-ts`
- All configuration is done in code, reducing the time to prototype/setup/launch. With MongoDB's built-in validation, you essentially add another "DevOps" step before you can use the database.
- Support for JSON Schema draft 7. MongoDB's validation is based on version 4.
- Feathers Schema don't have to wait for a round-trip to the database to validate the data.
- Feathers Schema can be used in the browser or on the server.

MongoDB's built-in validation does have built-in support for `bsonType` to force data to be stored as a specific BSON type once it passes validation. There's nothing keeping you from using both solutions together. It's not a use case that's documented, here.