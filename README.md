# image-transformation-directive

This directive enables you to add a transformation API to any field that returns an image URL.

## Install

```bash
npm install image-transformation-directive # NOT ACTUALLY A THING, YET!
```

## Setup

```ts
import { transformationDirective, ImgixProvider } from "image-transformation-directive";
import { makeExecutableSchema } from "@graphql-tools/schema";

const provider = new ImgixProvider({ ... });

const { transformationDirectiveTypeDefs, transformationDirectiveTransformer } =
  transformationDirective({ provider });

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
        image: "https://media.cms.com/photo123.png",
      }),
    },
  },
});

const transformedSchema = transformationDirectiveTransformer(schema);
```

## Usage

Now queries that fetch the `image` field for the `cart` query can contain arguments to change the size of the image.

```graphql
{
  cart(id: "wtf") {
    id
    image(width: 300, height: 300)
  }
}
```

## Adapters

Each adapter should follow a similar pattern to:

```ts
class ImageTransformationAdapter {
  transform(imageUrl, options) {
    // Implement image transformation logic specific to the adapter
    // and return the transformed image URL
    return `${imageUrl}?w=${options.width}&h=${options.height}`;
  }
}
```

### ImgixAdapter

```ts
class ImgixAdapter extends ImageTransformationAdapter {
  transform(imageUrl, options) {
    // Implement image transformation logic using Imgix service
    // and return the transformed image URL
  }
}
```
