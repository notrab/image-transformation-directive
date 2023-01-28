# image-transformation-directive

This directive enables you to add a transformation API to any field that returns an image URL.

## Install

```bash
npm install image-transformation-directive
```

## Setup

```ts
import { transformationDirective, ImgixProvider } from "image-transformation-directive";

const provider = new ImgixProvider({ ... });

const { transformationDirectiveTypeDefs, transformationDirectiveTransformer } =
  transformationDirective({ directiveName: "transformation", provider });

const schema = makeExecutableSchema({
  typeDefs: [
    transformationDirectiveTypeDefs,
    /* GraphQL */ `
      type Query {
        cart(id: ID!): Cart
      }
      type Cart {
        id: ID!
        image: String @transformation
      }
    `,
  ],
  resolvers: {
    Query: {
      cart: (_, { id }) => ({
        id,
        image: "photo.png",
      }),
    },
  },
});

schema = transformationDirectiveTransformer(schema);
```

## Usage

Now queries that fetch the `image` field for the `cart` query can contain arguments to change the size of the image.

```graphql
{
  cart(id: "wtf") {
    id
    image(width: 500, height: 600)
  }
}
```

## Adapters

* Imgix (WIP)
* Cloudinary (WIP)
