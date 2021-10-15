# CRUD (Create Read Update Delete)
We will start from basic and learn how to create, read, update, delete operations on our documents. Before we get started we need to understand what is documents or collections for understand better.  This post is more focused on the query part. If you want to clear your basics. I will highly recommend you to watch MongoDB universties [M001 MongoDB Basics](https://university.mongodb.com/ "M001 MongoDB Basics"). I will share the mongoose queries.

## What are Collections ?
Collections are similar to table in your sql. Collections have multiple documents. For example: `users, posts`. You should always use small letters for your collections and your collections name should be plural. A collections may have multiple documents.

## What are Documents ?
Documents are part of collections. Documents are in the form of BSON which is  similar to JSON but have some [difference](https://www.mongodb.com/json-and-bson "difference"). Each collection has a unique ObjectId like in Sql we have Primary Key which will increment for new entries. MongoDB generate a new ObjectId whenever a new document is saved. 

Example of a document

```js
{
    "_id" : ObjectId("615d4058eb420d2c2c568046"),
    "description" : "Hello, You are awesome",
    "status" : "active",
    "userId" : ObjectId("6124dc2ce8e408966a0803ee"),
    "createdAt" : ISODate("2021-10-06T06:21:12.638Z"),
    "updatedAt" : ISODate("2021-10-14T13:12:38.297Z"),
    "__v" : 0,
    "deleteReason" : "Mere marzi"
}
```

## Collections Structure
MongoDB is super flexible and save your keyboard when you get errors like this field does not exists. If you are trying to insert a filed which is not exists in your collection. It will create a new one instead of bothering you 🤯. But for consistency [mongoose](https://mongoosejs.com/ "mongoose") ORM for mongoDB is used and helps you to save correct data in the collection. You can clear your basics with mongo by check out this cool post. [Mongoose Schema Design](https://www.freecodecamp.org/news/introduction-to-mongoose-for-mongodb-d2a7aa593c57/ "Mongoose Schema Design")


## Enough! Show me how to do it
Okay! Enough talking. Let's get started 🚀.

#### Insert Document

```sql
INSERT INTO CUSTOMERS (ID,NAME,AGE,ADDRESS,SALARY)
VALUES (2, 'Khilan', 25, 'Delhi', 1500.00 );
```
In **mongoose** you will write like as shown below

```js
const userModel = requier('./models/users.model');

let user = {
	firstName: "Pankaj",
	lasrName: "Sharma",
	email: "pankajsharmavats88@gmail.com",
	portfolio: "http://pankaj-sharma-portfolio.netlify.app/",
	status: active
};

let userObj = new userModel(user);

// user.save() returns a promise it will return the saved document or throw an error
user.Objsave()
	.then(user => console.log(user))
	.catch(err => console.error(err));

```
Sometimes you also wan to add some extra fields in saved user object. If you try to do `user.profilePitcture = '';` It will not work. You need to convert the result in JSON. You have serveral options.

```js
user = JSON.stringify(user);
user = JSON.parse(user);
```

Or you can also call lean() method to convert mognoose result in plain object so you can add or remove fields.
```js
let user = await userObj.save().lean()
```

