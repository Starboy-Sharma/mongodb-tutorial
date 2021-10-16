# CRUD (Create Read Update Delete)
We will start from basic and learn how to create, read, updat and delete operations on our documents. Before we get started we need to understand what is documents or collections for understanding better.  This post is more focused on the query part. If you want to clear your basics. I will highly recommend you to watch MongoDB universities [M001 MongoDB Basics](https://university.mongodb.com/ "M001 MongoDB Basics"). I will share the mongoose queries.

## What are Collections?
Collections are similar to a table in your SQL. Collections have multiple documents. For example users, posts. You should always use small letters for your collections and your collections name should be plural. A collection may have multiple documents.

## What are Documents?
Documents are part of collections. Documents are in the form of BSON which is  similar to JSON but have some [differences](https://www.mongodb.com/json-and-bson "differences"). Each collection has a unique ObjectId like in SQL we have Primary Key which will increment for new entries. MongoDB generates a new ObjectId whenever a new document is saved. 

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
MongoDB is super flexible and saves your keyboard when you get errors like this field does not exist. If you are trying to insert a file that does not exist in your collection. It will create a new one instead of bothering you. But for consistency [mongoose,](https://mongoosejs.com/ "mongoose,") ORM for MongoDB is used and helps you to save correct data in the collection. You can clear your basics with mongo by checking out this cool post. [Mongoose Schema Design](https://www.freecodecamp.org/news/introduction-to-mongoose-for-mongodb-d2a7aa593c57/ "Mongoose Schema Design")


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
Sometimes you also want to add some extra fields in the saved user object. If you try to do `user.profilePitcture = '';` It will not work. You need to convert the result into JSON. You have various options.

```js
user = JSON.stringify(user);
user = JSON.parse(user);
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

All of this method will going to return promise and if you want to get udated document you need to pass 3rd parameter which will return the result with updated object.
```js
let user = await userModel.findByIdAndUpdate(userId, updateData, { new: true, });
```
If you want to do something like if condition is not match then insert a new document in that case you can pass upsert to true. It will insert a new document if condition not matched else it will going to update the matching document.
```js
let user = await userModel.findByIdAndUpdate(userId, updateData, { new: true,  upsert: true});
```
Also note when you are updating your document your schema validations will not affect here. If you want to validate your updated document by mongoose then you should pass runvalidation falg.
```js
let user = await userModel.findByIdAndUpdate(userId, updateData, { new: true,  upsert: true, runValidators: true});
```

#### Delete Document
Delete query in sql
```sql
DELETE FROM table_name WHERE condition; 
```
In mongoose we have following options:
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