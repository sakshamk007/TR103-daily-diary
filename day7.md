# Day 7

## 1. Domain Driven Design
1. Find primary domain and its sub-domains
1. Entities (real life analogy like products, users)
1. Object values (data dependent on enitites like name, description)
1. Aggregate (relation between enitites and object values like sales) 

## 2. Learnt about image compression using multer and sharp in express.js
1. Multer handles file uploads
1. Sharp performs image processing
1. Express serves the web application and static files.

```
const express = require("express");
const multer = require("multer");
const sharp = require("sharp");
const fs = require("fs");

const app = express();
const storage = multer.memoryStorage();
const upload = multer({ storage });

app.use(express.static("./uploads"));

app.get("/", (req, res) => {
  res.send("Image compression");
});

app.post("/", upload.single("picture"), async (req, res) => {
  fs.access("./uploads", (error) => {
    if (error) {
      fs.mkdirSync("./uploads");
    }
  });
  const { buffer, originalname } = req.file;
  const timestamp = new Date().toISOString();
  const ref = `${timestamp}-${originalname}.webp`;
  await sharp(buffer)
    .webp({ quality: 20 })
    .toFile("./uploads/" + ref);
  const link = `http://localhost:3000/${ref}`;
  return res.json({ link });
});

app.listen(3000);
```
