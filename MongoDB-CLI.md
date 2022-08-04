# MongoDB CLI

### Run mongoDB in docker container

```shell
docker run --rm --name no-sql -d mongo
docker exec -it no-sql sh
```

To start mongo cli type ```mongo``` inside docker container

#### MongoDB Commands

List all database ``` show dbs ``` 

Create database ``` use <database_name> ``` 

Show current database ``` db ``` 

###### Insert new data

```mongodb
db.products.insertOne({ 
    name: "iPhone 13", 
    price: 100000, 
    category: "smartphone", 
active: true});
db.products.find();
or
db.products.find().pretty();
```

###### Insert Multiple documents

```mongodb
db.products.insertMany([{ name: "Keyboard"}, { name: "Mouse" }]);
```



###### Find single data

```mongodb
db.products.find({ active: false, category: 'notebook' }, 
                 { active: 0}).pretty().limit(1).skip(1);

Note: Here active 0 means show all fields without active field. 
      Limit one means show one document.
      Skip one means skip first document.
```

###### Update document

```mongodb
db.products.updateOne({ name: "iPhone" }, { $set: { price: 120000 } });
db.products.updateMany({ active: true }, { $set: {active: true} );
Note: update is deprecated
```

###### Delete document

```mongodb
db.products.deleteOne({ name: "iPhone" });
db.products.deleteMany({ active: false });
Note: Remove is deprecated
```


