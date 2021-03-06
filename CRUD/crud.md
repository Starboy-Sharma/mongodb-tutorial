# CRUD (Create Read Update Delete)
We will start from basic queries and learn how to create, read, update and delete operations in MongoDB. Before we get started we need to understand what is documents or collections for understanding better.  This post is more focused on the query part. If you want to clear your basics. I will highly recommend you to watch MongoDB universities [M001 MongoDB Basics](https://university.mongodb.com/ "M001 MongoDB Basics"). I will share the mongoose queries.

## What are Collections?
Collections are similar to a table in your SQL. Collections have multiple documents. For example users, posts. You should always use small letters for your collections and your collections name should be plural. A collection may have multiple documents.

## What are Documents?
Documents are part of collections. Documents are in the form of BSON which is similar to JSON but have some [differences](https://www.mongodb.com/json-and-bson "differences"). Each collection has a unique ObjectId like in SQL we have Primary Key which will increment for new entries. MongoDB generates a new ObjectId whenever a new document is saved. 

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
}
```

## Collections Structure
MongoDB is super flexible and saves your keyboard from your hammer hands when you get errors like this field does not exist. If you are trying to insert a file that does not exist in your collection. It will create a new one instead of bothering you. But for consistency [mongoose,](https://mongoosejs.com/ "mongoose,") ORM for MongoDB is used and helps you to save correct data in the collection. You can clear your basics with mongo by checking out this cool post. [Mongoose Schema Design](https://www.freecodecamp.org/news/introduction-to-mongoose-for-mongodb-d2a7aa593c57/ "Mongoose Schema Design")


## Enough! Show me how to do it
Okay! Enough talking. Let's get started ????.

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
userObj.save()
	.then(user => console.log(user))
	.catch(err => console.error(err));

// you can also use await
let user = await userObj.save();

// with the lean()
let user = await userObj.save().lean();

```
Sometimes you also want to add some extra fields in the saved user object. If you try to do `user.profilePitcture = '';` It will not work. You need to convert the result into JSON. You have various options.

```js
let user = await userObj.save();

// modify mongoose result object
user = JSON.stringify(user);
user = JSON.parse(user);

// add a custom key
user.isSubscribed = false;

// delete a key
delete user.password;
```

Or you can also call the lean() method to convert mongoose results in the plain object so you can add or remove fields.

```js
let user = await userObj.save().lean()
```

    Model.create() is a shortcut for saving one or more documents to the database.

    MyModel.create(docs) does new MyModel(doc).save() for every doc in docs.

    This function triggers the following middleware.

    save()

#### Update Document
In SQL our query looks like this:
```sql
UPDATE table_name
SET column1 = value1, column2 = value2...., columnN = valueN
WHERE [condition];
```
Mongoose has following methods
- findByIdAndUpdate( userId, updateData , options)
- findOneAndUpdate( {  status: 'active' }, updateData, options)
- updateMany({  status: 'active' } , updateData, options)


All of these methods will be going to return a promise and if you want to get an updated document you need to pass 3rd parameter which will return the result with an updated object.
```js
let user = await userModel.findByIdAndUpdate(userId, updateData, { new: true, });
```
If you want to do something like if the condition does not match then insert a new document in that case you can pass upsert to true. It will insert a new document if the condition is not matched else it will going to update the matching document.
```js
let user = await userModel.findByIdAndUpdate(userId, updateData, { new: true,  upsert: true});
```
Also note when you are updating your document your schema validations will not affect here. If you want to validate your updated document by mongoose then you should pass runValidators falg.
```js
let user = await userModel.findByIdAndUpdate(userId, updateData, { new: true,  upsert: true, runValidators: true});
```

#### Delete Document
Delete query in SQL
```sql
DELETE FROM table_name WHERE condition; 
```
In mongoose we have the following options:
```

findOneAndDelete() returns the deleted document after having deleted it (in case you need its contents after the delete operation);
deleteOne() is used to delete a single document
remove() is a deprecated function and has been replaced by deleteOne() (to delete a single document) and deleteMany() (to delete multiple documents)

findByIdAndDelete() should be able to delete on _id.
findOneAndDelete(query) delete single document which match te given query and return the deleted document.
```
```js
let user = await userModel.findByIdAndDelete(userId);

// delete user those age is 20 or less than 20
let user = await userModel.delete({ age: { $lte: 20 } });
```

#### Select Document
In sql we will like our sql statement like this
```sql
SELECT * FROM table_name;
```

In mongoose, we have a find method that is used to pick data from collections. For example, if you have a user collection and you want to select all the data from the database. You need the `find()` method which takes an object as a parameter. You conditional part of the query is basically live in first parameter of `find( {< Condition >}, { <Projection> } )`
```js
let usersData = await users.find( {} ); // return all the documents inside users collection
```

What if you are looking for a conditional base result. For example you only want to select active users.
```js
let userData = await users.find( { status: 'active' } ); // return active users
```

Users which status are active and their age is above or equal 18 and below 65
```js
// $gte = greater than or equal to
// $lt = less than
let userData = await users.find( { status: 'active', age: { $gte: 18 }, age: { $lt: 65 } } );
```
If you run these queries you will get all the fields of your document. If you want to select some required fields. You can also do this. In SQL, you write like this
```sql
SELECT id, email, name FROM table_name 
```
In mongoose 
```js
let userData = await users.find(
    { status: 'active' }, // Similar to where clause
    { _id: 1, name: 1, email: 1 } // Selected data
);
```

**Note** By default _id field will come as a selected field if you do not want to add _id you can simply write `_id: 0`. So, MongoDB will skip _id.
If don't want to select any specific field. For example I want to select all fields except name field. So I can write something like this
```js
let userData = await users.find(
    { status: 'active' }, // Similar to where clause
    { name: 0 } // Selected data
);
```
