<div align="center">
<h1>Node.js Basic</h1>
</div>

## Some Yarn Commands

```shell
# Install global package
sudo yarn global add nodemon --prefix /usr/local 
# Install local package
yarn add date-fns
# Install Dev Dependency
yarn add nodemon -D

# List installed package
yarn list

# enable yarn emoji
yarn config set -- --emoji true
```

## Color ```console.log```

```js
// in browser
console.log("%cHello World", "background : white; color: black");

// bash
console.log("\x1B[34mHello World\x1B[m");
30m => grey
31m => red
32m => green
34m => blue
```



## OS module

```js
const os = require("os");

console.log(os.type());
console.log(os.version());
console.log(os.homedir());
console.log(os.hostname());
```

## PATH module

```js
const path = require("path");

console.log(path.dirname(__filename));
console.log(path.basename(__filename));
console.log(path.extname(__filename));
console.log(path.parse(__filename));
```

## FS module

###### Read and Write file using async and callback

```js
const fs = require("fs");

fs.readFile(path.join(__dirname, "files", "starter.txt"), 
            "utf-8", (err, data) => {
    if (err) console.log(err.message);
    console.log(data);
});

fs.writeFile(path.join(__dirname, "files", "reply.txt"), 
            "Nice to meet you", (err) => {
    if (err) console.log(err.message);
    console.log("Write complete");

    fs.appendFile(path.join(__dirname, "files", "reply.txt"), 
                    "\n\nNew append line", (err) => {
        if (err) console.log(err.message);
        console.log("Append complete");

        fs.rename(path.join(__dirname, "files", "reply.txt"), 
                path.join(__dirname, "files", "newReply.txt"), (err) => {
            if (err) console.log(err.message);
            console.log("Rename complete");
        });
    });
});
```

###### Read and Write file using Promise

```js
const fsPromises = require("fs").promises;

const fileOps = async () => {
    try {
        const data = await fsPromises.readFile(
            path.join(__dirname, "files", "starter.txt"), 
            "utf-8");
        console.log(data);
        await fsPromises.unlink(
            path.join(__dirname, "files", "starter.txt"));

        await fsPromises.writeFile(
            path.join(__dirname, "files", "promiseWrite.txt"), 
            data);
        await fsPromises.appendFile(
            path.join(__dirname, "files", "promiseWrite.txt"), 
            "\n\nAppended txt goes here.");
        await fsPromises.rename(
            path.join(__dirname, "files", "promiseWrite.txt"), 
            path.join(__dirname, "files", "promiseComplete.txt"));

        const newData = await fsPromises.readFile(
            path.join(__dirname, "files", "promiseComplete.txt"), 
            "utf-8");
        console.log(newData);
    } catch (error) {
        console.error(error.message);
    }
};
fileOps();
```

###### Read and Write big file using Stream

```js
const fs = require("fs");

const rs = fs.createReadStream("./files/lorem.txt", 
                                { encoding: "utf-8" });
const ws = fs.createWriteStream("./files/new-lorem.txt");

// one way
rs.on("data", (dataChank) => {
    ws.write(dataChank);
    console.log(dataChank);
});

// more efficient
rs.pipe(ws);
```

###### Create and Remove directory

```js
const fs = require("fs");

// create dir if not exists
if (!fs.existsSync("./new")) {
    fs.mkdir("./new", (err) => {
        if (err) console.log(err.message);
        console.log("Directory Created");
    });
}

// remove dir if exists
if (fs.existsSync("./new")) {
    fs.rmdir("./new", (err) => {
        if (err) console.log(err.message);
        console.log("Directory removed");
    });
}
```

###### Event Emitter

```js
const EventEmitter = require("events");

class MyEmitter extends EventEmitter {}

// initialize object
const myEmitter = new MyEmitter();

// add listener for the log event
myEmitter.on("log", (msg) => console.log(msg));

setTimeout(() => {
    // Emit event
    myEmitter.emit("log", "Log event emitted!");
}, 2000);
```

## MongoDB and Mongoose

###### Connect MongoDB

```js
// connect.js
const mongoose = require("mongoose");

const connectDB = (url) => {
    return mongoose.connect(url, {
        useNewUrlParser: true,
        useCreateIndex: true,
        useFindAndModify: false,
        useUnifiedTopology: true,
    });
};

module.exports = connectDB;

// app.js
const start = async() => {
    try {
        await connectDB(process.env.MONGO_URI);
        app.listen(PORT, () => {
            console.log(`Server is listening on port ${PORT}`);
        })
    } catch (error) {
        console.error(error)
    }
}
start();
```

###### Models using Mongoose

```js
const mongoose = requrie("mongoose");

const TaskSchema = new mongoose.Schema({
    name: {
        type: String,
        require: [true, 'must provide name'],
        trim: true,
        maxlength: [20, 'name can not be more that 20 characters']
    }
});

moduels.exports = mongoose.model('Task', TaskSchema);
```

###### CRUD operation

```js
// Get 
await Task.findOne({ _id: taskId }).select({ __v: 0 });

// post
await Task.create(req.body);

// put
await Task.findOneAndUpdate({ _id: taskId }, req.body, {
    new: true,
    runValidators: true,
    useFindAndModify: false,
    // overwrite: true
})

// delete
await Task.findOneAndDelete({ _id: taskId }).select({ __v: 0 });
```
