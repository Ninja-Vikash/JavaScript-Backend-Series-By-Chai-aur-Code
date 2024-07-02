## Complete JavaScript Backend Setup - Chai aur Code ☕

### Reference : <a href="https://www.youtube.com/@chaiaurcode" target="_blank">*Chai aur Code*</a>  💖<br/>

**YouTube**<br/>
<a href="https://youtu.be/7fjOw8ApZ1I?si=YvCPzVHbJr0-3KcZ" target="_blank">Complete Backend Developer course | Part 1</a> <br/>
<a href="https://youtu.be/8k-kK3tsJFY?si=BnhIulAJjI7XarBN" target="_blank">Complete Backend Developer course | Part 2</a>

**About**<br/>
This documentation is based on the *Complete JavaScript Backend Series* from the *Chai aur Code* YouTube Channel.<br/>
Thank you! **Hitesh Sir**, for providing us with such amazing content for free! 🙏✨

Checkout the videos for better explanation 👆

## Getting Started 🚀
To write code we need a directory, where we can start our work.<br/>
Create a directory first for example `backend`

Drag it or open it using <a href="https://code.visualstudio.com/" target="_blank">**Visual Studio Code (VS Code)**</a><br/>
> [!NOTE]\
> **Visual Studio Code** (VS Code) is a free, open-source Integrated Development Environment (**IDE**) developed by **Microsoft**, offering features like **debugging**, **version control**, and extensions to support various programming languages.

### Follow the steps
**Initialize with npm**
```bash
npm init
```
It will ask some questions, answer them accordingly based upon your project.

**Create files in root directory**
```bash
touch .env .gitignore .prettierrc .prettierignore
```
**Create further directories and files**<br/>
Run the following commands one by one
```bash
mkdir public src

cd src

# Run the command for files
touch index.js app.js constants.js

# Run the command for directories
mkdir controllers db models middlewares routes utils
```

> [!IMPORTANT]
> ```bash
> mkdir folder1 folder2
> ```
> Won't work in Windows\
> For execute the command you can use `git-bash`\
> Or you can create them manually. 😁

**Install dev dependencies**
```bash
npm i -D nodemon

npm i -D prettier
```
> [!NOTE]\
> `Nodemon` used for hot reloading for every single change in files.\
> `Prettier` used to configure the formatting rules.\
> `-D` is a flag to save them as a dev dependency

Reference:  <a href="https://www.npmjs.com/package/prettier" target="_blank">**prettier**</a> | <a href="https://www.npmjs.com/package/nodemon" target="_blank">**nodemon**</a>


**Install dependencies**
```bash
npm i express dotenv mongoose
```

Reference: <a href="https://expressjs.com/" target="_blank">**express**</a> | <a href="https://www.npmjs.com/package/dotenv" target="_blank">**dotenv**</a> | <a href="https://www.npmjs.com/package/mongoose" target="_blank">**mongoose**</a>

***
### Code Updates 🤔
`package.json`
```json
"type": "module",
```
```json
"scripts": {
    "dev": "nodemon src/index.js"
},
```

`.gitignore` -> <a href="https://mrkandreev.name/snippets/gitignore-generator/" target="_blank">gitignore builder</a>


`.prettierrc`
```json
{
    "singleQuote": false,
    "bracketSpacing": true,
    "tabWidth": 2,
    "trailingComma": "es5",
    "semi": true
}
```

`.prettierignore`
```
/.vscode
/node_modules
./dist

*.env
.env
.env.*
```

`.env`
```js
PORT=8000
MONGODB_URI=mongodb+srv://<username>:<password>@cluster0.cp5kxxy.mongodb.net/?retryWrites=true&w=majority&appName=Cluster0
CORS_ORIGIN=*
```
> [!IMPORTANT]\
> Don't include any special character in your password\
> eg. `@`, `&` etc

#### Config of environment variable
```js
import dotenv from "dotenv"

dotenv.config({
    path: "./env"
})
```
> [!WARNING]\
> This is not a valid method to use `import` syntax with `dotenv`
> 
> Let's config the `package.json` to use it as a experimental method
> ```json
> "scripts": {
>   "dev": "nodemon -r dotenv/config --experimental-json-modules src/index.js"
> },

***

### Code snippets
> [!IMPORTANT]\
> **Resolve errors**\
> By Providing the complete path in `import` method, which means with extensions. So then we can resolve errors
> ```js
> import connectDB from "./db/index.js";
> //...
> 
> import { DB_NAME } from "../constants.js";
> //...
> ```

`src/db/index.js`
```js
import mongoose from "mongoose";
import { DB_NAME } from "../constants.js";

const connectDB = async () => {
    try {
        const connectionInstance = await mongoose.connect(`${process.env.MONGODB_URI}/${DB_NAME}`)
        console.log(`\n MongoDB connected !! DB HOST: ${connectionInstance.connection.host}`)
    } catch (error) {
        console.error("MONGODB connect FAILED ", error)
        process.exit(0)
    }
}

export default connectDB;
```
> [!NOTE]\
> `DB_NAME` is a constant variable
>
> `src/constants.js`
> ```js
> export const DB_NAME = "test"
> ```


`src/index.js`
```js
import dotenv from "dotenv"
import connectDB from "./db/index.js";
import { app } from "./app.js";

dotenv.config({
    path: "./env"
})

connectDB()
.then(()=>{
    app.listen(process.env.PORT || 8000, ()=>{
        console.log(`\n Server is running at port ${process.env.PORT}`)
    })
})
.catch((err)=>{
    console.log("MONGODB Connection failed !!! ", err )
})
```

### Use of `middlewares`
`middlewares` are used modifying **request** and **response** objects, ending the request-response cycle and calling the next middleware function in the stack.<br/>
Usually created with `use` keyword.
```js
app.use((err, req, res, next) => {
    // Some code to be executed  
});
```
> [!NOTE]\
> `middlewares` have 4 parameters
> 
> `err` The error object that was thrown or passed to the next function with an error argument.\
> `req` The request object.\
> `res` The response object.\
> `next` The next middleware function in the stack.

**Install dependencies**
```bash
npm i cookie-parser cors
```

Reference: <a href="https://www.npmjs.com/package/cookie-parser" target="_blank">**cookie-parser**</a> | <a href="https://www.npmjs.com/package/cors" target="_blank">**cors**</a>

`src/app.js`
```js
import express from "express"
import cors from "cors"
import cookieParser from "cookie-parser"

const app = express()

app.use(cors({
    origin: process.env.CORS_ORIGIN,
    credentials: true
}))

app.use(express.json({ limit: "16kb" }))
app.use(express.urlencoded({ extended: true, limit: "16kb" }))
app.use(express.static("public"))
app.use(cookieParser())


export { app }
```
***

#### `asyncHandler( )`
A utility is used for handling all requests and responses on every route<br/>
`src/utils/asyncHandler.js`
```js
const asyncHandler = (requestHandler)=> {
    return (req, res, next) => {
        Promise.resolve(requestHandler(req, res, next)).catch((err) => next(err))
    }
}

export { asyncHandler }
```
> [!IMPORTANT]\
> Save this utility\
> Reusuable method we will use it more often

`src/utils/ApiError.js`
```js
class ApiError extends Error {
    constructor(
        statusCode,
        message = "Something went wrong",
        errors = [],
        stack = ""
    ) {
        super(message)
        this.statusCode = statusCode
        this.data = null
        this.message = message
        this.success = false
        this.errors = errors

        if (stack) {
            this.stack = stack
        } else {
            Error.captureStackTrace(this, this.constructor)
        }
    }
}

export { ApiError }
```
> [!NOTE]\
> Custom API Error response is very useful for defining.\
> The error fields to appear.

`src/utils/ApiResponse.js`
```js
class ApiResponse {
    constructor(statusCode, data, message="success"){
        this.statusCode = statusCode,
        this.data = data,
        this.message = message,
        this.success = statusCode < 400
    }
}
```
HTTP statusCode Responses<br/>
Reference: <a href="https://developer.mozilla.org/en-US/docs/Web/HTTP/Status" target="_blank">**MDN**</a>

***

#### Data Modeling
`src/model/user.model.js`
```js
import mongoose, { Schema } from "mongoose";

const userSchema = new Schema({});

export const User = mongoose.model("User", userSchema);
```

> [!NOTE]\
> `user.model.js` helps to identify the file by its filename.\
> eg. the file is a model file. 🤓
>
> 
> In models we store data in the form of Schemas\
> For creating a **Schema** we use `new` keyword, Since Schema is a method which accepts an Object
> ```js
> import mongoose from "mongoose";
> 
> new mongoose.Schema({});
> ```
> Or we can import `Schema` object for creating Schema
> ```js
> import mongoose, { Schema } from "mongoose";
> 
> new Schema({});
> ```
> Store it in a variable and export it
> ```js
> import mongoose, { Schema } from "mongoose";
> 
> const userSchema = new Schema({});
> 
> export User = mongoose.model("User", userSchema);
> ```
> `mongoose.model("User", userSchema)` has two arguments: the **collection name** and the **schema type**.\
> In MongoDB, the collection name becomes lowercase and plural. For example, **User** becomes **users**.

#### How to create data fields?
We can simply create a data field as given.
```js
const userSchema = new Schema({
        username: String
})
```

But we will follow a best practice
```js
const userSchema = new Schema({
        username: {
            type: String,
            required: true,
            unique: true,
            lowercase: true
        }
})
```

Connection between two data models
```js
// User Data Model
const userSchema = new Schema({
        username: {
            type: String,
            required: true,
            unique: true,
            lowercase: true,
        },
        email: {
            type: String,
            required: true,
            unique: true,
        }
})

export const User = mongoose.model("User", userSchema)
```
```js
// CartList Data Model
const productSchema = Schema({
    name: {
        type: String,
        required: true
    },
    price: {
        type: String,
        required: true,
        default: 0
    }
})

const cartModel = Schema({
        username: {
            type: mongoose.Schema.Types.ObjectId,
            ref: "User"
        },
        products: [ productSchema ],
        status: {
            type: String,
            enum: ["PENDING", "CANCELLED", "DELIVERED"].
            default: "PENDING"
        }
}, { timestamps: true })

export const Cart = mongoose.model("Cart", cartModel)
```
> [!IMPORTANT]\
> When we connect two data models, we often use fields like type and ref to establish relationships between them
> ```js
> username: {
>     type: mongoose.Schema.Types.ObjectId,
>     ref: "User"
> },
> ```
> We want the **username** from the `User` model.\
> We need to use the `type` key to connect and the `ref` key\
> The `ref` key holds the value of the **collection name** of the parent data model.\
> Since the collection name of the parent is `User`, that is why we are referring to it in the child data model.
>
> ```js
> products: [ productSchema ],
> ```
> In products, we will use an `array` type because a user may purchase one or more products.
> ```js
> status: {
>     type: String,
>     enum: ["PENDING", "CANCELLED", "DELIVERED"].
>     default: "PENDING"
> }
> ```
> The `status` field has an `enum` key, which provides **constant** values to be chosen by users.
> ```js
> const cartModel = Schema({ }, { timestamps: true })
> ```
> In Mongoose, the schema constructor accepts a second object to enable `timestamps`\
> Which automatically adds `createdAt` and `updatedAt` fields to your schema to track document creation and modification times.

***

#### Mongoose Aggregation Pipelines
```bash
npm i mongoose-aggregate-paginate-v2
```

Reference: <a href="https://www.npmjs.com/package/mongoose-aggregate-paginate-v2" target="_blank">**mongoose-aggregate-paginate-v2**</a> | <a href="https://www.mongodb.com/docs/manual/core/aggregation-pipeline/" target="_blank">**mongoDB aggregation pipeline**</a>

`src/models/video.model.js`
```js
import mongoose, { Schema } from "mongoose";
import mongooseAggregatePaginate from "mongoose-aggregate-paginate-v2";

// videoSchema code
// ...
// ...

videoSchema.plugin(mongooseAggregatePaginate)

export const Video = mongoose.model("Video", videoSchema)
```
> [!NOTE]\
> `videoSchema.plugin(mongooseAggregatePaginate)` helps to write aggregation pipelines

#### Packages for encrypting data
```bash
npm i bcrypt jsonwebtoken
```
Reference: <a href="https://www.npmjs.com/package/bcrypt" target="_blank">**bcrypt**</a> | <a href="https://www.npmjs.com/package/jsonwebtoken" target="_blank">**jwt**</a>

`src/models/user.model.js`
```js
import bcrypt from "bcrypt";

// userSchema code
// ...
// ...

userSchema.pre("save", async function(next){
    if(!this.isModified("password")) return next();

    this.password = bcrypt.hash(this.password, 10)
    next()
})

userSchema.methods.isPasswordCorrect = async function(password){
    return await bcrypt.compare(password, this.password)
}

export const User = mongoose.model("User", userSchema)
```
> [!IMPORTANT]\
> `userSchema.pre("event", callbackfn())`\
> pre is a `hook` or `method` which accepts two values as: **Event** and a **CallBack_fn()**\
> Since bcrypting is a time consuming task. For safety we will use a **async** function in callback.\
> The callback function acts here as a middleware, So it must have `next()` flag to propagate to next node.\
> Similarly, we can create custom methods using the Schema
> ```js
> userSchema.methods.isPasswordCorrect = async function(){
>    // Custom method code...      
> }
> ```

> [!WARNING]\
> We can't use an arrow function here, because **arrow function doesn't have access of `this` keyword**.

#### JWT ( JsonWebTokens )
`generateAccessToken`
```js
userSchema.methods.generateAccessToken = function(){
    return jwt.sign(
        {
            _id: this._id,
            email: this.email,
            username: this.username,
            fullname: this.fullname
        },
        process.env.ACCESS_TOKEN_SECRET,
        {
            expiresIn: process.env.ACCESS_TOKEN_EXPIRY
        }
    )
}
```

`generateRefreshToken`
```js
userSchema.methods.generateRefreshToken = function(){
    return jwt.sign(
        {
            _id: this._id,
        },
        process.env.REFRESH_TOKEN_SECRET,
        {
            expiresIn: process.env.REFRESH_TOKEN_EXPIRY
        }
    )
}
```

> [!NOTE]\
> `jwt.sign({payload}, secret, {expiry})`\
> Refresh Token has less payload compare to Access Token.
> 
> Create environment variables for `ACCESS_TOKEN_SECRET`, `ACCESS_TOKEN_EXPIRY`, `REFRESH_TOKEN_SECRET` & `REFRESH_TOKEN_EXPIRY`