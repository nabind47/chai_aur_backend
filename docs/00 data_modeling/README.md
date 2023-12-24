# [Data Modeling](https://www.youtube.com/watch?v=VbGl3msgce8&list=PLu71SKxNbfoBGh_8p_NS-ZAh6v7HhYqHW&index=5&ab_channel=ChaiaurCode)

## Ecommerce Data Modeling

```js
import mongoose from "mongoose";

const userSchema = new mongoose.Schema(
  {
    username: { type: String, required: true, unique: true, lowercase: true },
    email: { type: String, required: true, unique: true, lowercase: true },
    password: { type: string, required: true },
  },
  { timestamps: true }
);

export const User = mongoose.model("User", userSchema);
```

```js
import mongoose from "mongoose";

const categorySchema = new mongoose.Schema(
  {
    name: { type: String, required: true },
  },
  { timestamps: true }
);

export const Category = mongoose.model("Category", categorySchema);
```

```js
import mongoose from "mongoose";

const productSchema = new mongoose.Schema(
  {
    name: { required: true, type: String },
    description: { required: true, type: String },
    productImage: { type: String },
    price: { type: Number, default: 0 },
    stock: { default: 0, type: Number },
    category: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Category",
      required: true,
    },
    owner: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User",
      required: true,
    },
  },
  { timestamps: true }
);

export const Product = mongoose.model("Product", productSchema);
```

```js
import mongoose from "mongoose";

const orderItemSchema = new mongoose.Schema({
  quantity: { type: Number, required: true },
  productId: {
    type: mongoose.Schema.Types.ObjectId,
    ref: "Product",
  },
});

const orderSchema = new mongoose.Schema(
  {
    orderPrice: { type: Number, required: true },
    customer: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "User",
    },
    orderItems: { type: [orderItemSchema] },
    address: { type: String, required: true },
    status: {
      type: String,
      enum: ["PENDING", "CANCELLED", "DELIVERED"],
      default: "PENDING",
    },
  },
  { timestamps: true }
);

export const Order = mongoose.model("Order", orderSchema);
```

## Hospital Management

```js
import mongoose from "mongoose";

const doctorSchema = new mongoose.Schema(
  {
    name: { type: String, required: true },
    salary: { type: String, required: true },
    experienceInYears: { type: Number, defaut: 0 },
    qualification: { type: String, required: true },
    worksInHospitals: [
      {
        type: mongoose.Schema.Types.ObjectId,
        ref: "Hospital",
      },
    ],
  },
  { timestamps: true }
);

export const Doctor = mongoose.model("Doctor", doctorSchema);
```

```js
import mongoose from "mongoose";

const hospitalSchema = new mongoose.Schema(
  {
    name: { type: String, required: true },
    addressLine1: { type: String, required: true },
    addressLine2: { type: String },
    city: { type: String, required: true },
    pincode: { type: String, required: true },
    specializedIn: [{ type: String }],
  },
  { timestamps: true }
);

export const Hospital = mongoose.model("Hospital", hospitalSchema);
```

```js
import mongoose from "mongoose";

const patientSchema = new mongoose.Schema(
  {
    name: { type: String, required: true },
    age: { type: Number, required: true },
    address: { type: String, required: true },
    bloodGroup: { type: String, required: true },
    diagonsedWith: { type: String, required: true },
    gender: {
      type: String,
      enum: ["M", "F", "O"],
      required: true,
    },
    admittedIn: {
      type: mongoose.Schema.Types.ObjectId,
      ref: "Hospital",
    },
  },
  { timestamps: true }
);

export const Patient = mongoose.model("Patient", patientSchema);
```

```js
import mongoose from "mongoose";

const medicalRecordSchema = new mongoose.Schema({}, { timestamps: true });

export const MedicalRecord = mongoose.model(
  "MedicalRecord",
  medicalRecordSchema
);
```
