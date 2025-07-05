# Script to create Properties and Types

1. First run `pnpm create-private-key` to create a private key and address in the .env file
2. Take the address and enter it in Faucet to get some testnet ETH [https://faucet.conduit.xyz/geo-test-zc16z3tcvf](https://faucet.conduit.xyz/geo-test-zc16z3tcvf)
3. Update the code in `index.ts` to create the properties and types you want to create
4. Run `pnpm create-types` to create the properties and types
5. Query the Indexer API to verify your type exists. Replace the logged ID from the type of the `index.ts` script. see [https://v2-postgraphile.up.railway.app/graphql?query=%7B%0A++type%28id%3A+%22f47ac10b-58cc-4372-a567-0e02b2c3d479%22%29+%7B%0A++++name%0A++%7D%0A%7D](https://v2-postgraphile.up.railway.app/graphql?query=%7B%0A++type%28id%3A+%22f47ac10b-58cc-4372-a567-0e02b2c3d479%22%29+%7B%0A++++name%0A++%7D%0A%7D).

## How to create a text property

```typescript
const { id: summaryIdPropertyId, ops: createSummaryPropertyOps } =
  Graph.createProperty({
    name: "Summary",
    dataType: "TEXT",
  });
```

If you want a different data type e.g.

- number you can provide `dataType: 'NUMBER'`.
- boolean you can provide `dataType: 'CHECKBOX'`.
- datedatem you can provide `dataType: 'TIME'`.

## How to create a Movie type

This one will use the already available properties `name` and `description` and the `summaryIdPropertyId` property we created earlier.

```typescript
import { SystemIds } from "@graphprotocol/grc-20";

const { id: movieTypeId, ops: createMovieTypeOps } = Graph.createType({
  name: "Movie",
  properties: [
    SystemIds.NAME_PROPERTY,
    SystemIds.DESCRIPTION_PROPERTY,
    summaryIdPropertyId,
  ],
});
```

## How to create a relation

We create a Genre type with only a name property and a Genre property and then a Movie type that has a relation to the Genre type.

### Create Genre

```typescript
const { id: genreTypeId, ops: createGenreTypeOps } = Graph.createType({
  name: "Genre",
  properties: [SystemIds.NAME_PROPERTY],
});
```

```typescript
const { id: genreIdPropertyId, ops: createGenrePropertyOps } =
  Graph.createProperty({
    name: "Genre",
    dataType: "RELATION",
    relationValueTypes: [genreTypeId],
  });
```

## Create Movie

```typescript
import { SystemIds } from "@graphprotocol/grc-20";

const { id: movieTypeId, ops: createMovieTypeOps } = Graph.createType({
  name: "Movie",
  properties: [
    SystemIds.NAME_PROPERTY,
    SystemIds.DESCRIPTION_PROPERTY,
    summaryIdPropertyId,
    genreIdPropertyId,
  ],
});
```
