---
layout: post
title: "Geospatial queries with mongoose and TypeScript"
date: 2021-01-05
description: "Avoiding Harvesine formula when calculating the distance between two points with mongoose's <code>$geometry</code> and <code>$maxDistance</code> operators."
color: "#2F74C0"
---

## Scenario

I was recently working on a problem where I needed to query messages based on a certain physical location.

To achieve that, a Haversine formula implementation is used to calculate the distance between two points using its latitude and longitude. However, we can solve this problem by query a schema using geospatial's query feature from Mongoose.

## Approach

First, we need to declare in the a MessageSchema and what field should be used as `2dSphere`:

```typescript
// message.model.ts

import mongoose from "mongoose";

export type MessageDocument = mongoose.Document & {
  name: string;
  text: string;
  location: {
    type: { type: String };
    coordinates: Array;
  };
};

const MessageSchema = new mongoose.Schema(
  {
    name: String,
    text: String,
    location: {
      type: { type: String },
      coordinates: Array,
    },
  },
  {
    timestamps: {},
  }
);

MessageSchema.index({ location: "2dsphere" });

export default mongoose.model<MessageDocument>("Message", MessageSchema);
```

Then, we can query `Message` objects by latitude and longitude via the `$maxDistance` and `$geometry` MongoDB operators, passing the longitude and latitude as method parameters to the service:

```javascript
// message.service.ts

import Message from "../schemas/MessageSchema";

interface IRequest {
  latt: number;
  long: number;
}

class MessageService {
  private messageSchema;

  constructor(private messageSchema) {}

  async run({ latt, long }:IRequest): Promise<Message[]> {

    const messages = await this.messageSchema.find({
      location: {
        $near: {
          $maxDistance: 15000,
          $geometry: {
            type: "Point",
            coordinates: [long, latt],
          },
        },
      },
    });

    return messages;
  }
}
```

## Conclusion

Now, `MessageService` is able to query messages by latitude and longitude.

**My takeaway from this is:** don't re-invent the wheels. Use tools that are going to make you solve problems faster.
