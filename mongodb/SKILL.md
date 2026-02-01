---
name: mongodb
description: Work with MongoDB databases using best practices. Use when designing schemas, writing queries, building aggregation pipelines, or optimizing performance. Triggers on MongoDB, Mongoose, NoSQL, aggregation pipeline.
license: MIT
source: hoodini/ai-agents-skills
---

# MongoDB & Mongoose

## Quick Start

```typescript
import mongoose from 'mongoose';

await mongoose.connect(process.env.MONGODB_URI!, {
  maxPoolSize: 10,
});
```

## Schema Design

```typescript
const userSchema = new Schema<IUser>({
  email: {
    type: String,
    required: true,
    unique: true,
    lowercase: true,
  },
  name: { type: String, required: true },
  password: { type: String, required: true, select: false },
  role: { type: String, enum: ['user', 'admin'], default: 'user' },
}, { timestamps: true });

userSchema.index({ email: 1 });
```

## Query Operations

```typescript
// Find with filters
const users = await User.find({
  role: 'user',
  createdAt: { $gte: new Date('2024-01-01') },
}).select('name email').sort({ createdAt: -1 }).limit(10).lean();

// Update
await User.findByIdAndUpdate(userId, { $set: { name: 'New Name' } }, { new: true });
```

## Aggregation Pipeline

```typescript
const results = await Order.aggregate([
  { $match: { status: 'completed' } },
  { $group: {
    _id: '$customerId',
    totalOrders: { $sum: 1 },
    totalSpent: { $sum: '$total' },
  }},
  { $sort: { totalSpent: -1 } },
  { $limit: 10 },
]);
```

## Transactions

```typescript
const session = await mongoose.startSession();
try {
  session.startTransaction();
  await User.create([{ name: 'Alice' }], { session });
  await session.commitTransaction();
} catch (error) {
  await session.abortTransaction();
} finally {
  session.endSession();
}
```

## Resources

- **MongoDB Docs**: https://www.mongodb.com/docs/
- **Mongoose Docs**: https://mongoosejs.com/docs/
