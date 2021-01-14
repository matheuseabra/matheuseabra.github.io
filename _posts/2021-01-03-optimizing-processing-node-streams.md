---
layout: post
title: "Processing large datasets in chunks with Node.js"
date: 2021-01-03
description: "How to use the readline module in order to process large datasets in chunks and thus not blocking the main thread."
color: "#8ac800"
---

One use case I generally see is reading the contents of files. With Node that is usually done with the method `fs.readFile()` from the `fs` native module.

To read a small file, it's okay to use the approach below:

```javascript
const fs = require("fs");

fs.readFile("./small-text.txt", "utf-8", (err, contents) => {
  if (err) {
    throw err;
  }
  console.log(contents);
});
```

Then you should be able to read this small file content. However, for larger file sizes, you would encounter an error with the buffer. Such as `RangeError: Attempt to allocate Buffer larger than maximum size.`

**Solution:** In order to read the large files, you can import the `readline` native module.

```javascript
const fs = require("fs");
const readline = require("readline");

const lineReader = readline.createInterface({
  input: fs.createReadStream("path/largeFile.csv"),
  output: process.stdout,
  terminal: false,
});

lineReader.on("line", (line) => {
  console.log(line);
});

lineReader.on("pause", () => {
  console.log("Done processing the file!");
});
```

As described in the [node documentation](https://nodejs.org/api/readline.html):

> "The readline module provides an interface for reading data from a Readable stream one line at a time."

**Note:** the Node.js application will not terminate until the readline.Interface is closed because the interface waits for data to be received on the input stream.

Replace the file path with your path to the large file to process. You can process the file line by line inside the `.on('line')` event, such as parsing to JSON and increment the counter. The final sum can be displayed at the `.on('pause')` event after finish reading the file.

Now you should be able to process massive datasets! 🔥
