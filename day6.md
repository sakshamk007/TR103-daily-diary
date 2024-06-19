# Day 6

## 1. Project discussion - Online Bidding Platform
1. ideation
1. designed user roles - job creater and bidder
1. created logic of job creater and bidder

## 2. Learnt about creating routes using Router() in express.js

### app.js
```
const express = require('express')

const homeRoute = require('./app/routes/home')
const aboutRoute = require('./app/routes/about')
const loginRoute = require('./app/routes/login')

const app = express()
const port = 3000

app.use('/', homeRoute)
app.use('/', aboutRoute)
app.use('/', loginRoute)

app.listen(port, ()=>{
    console.log(`Listening app on port http://localhost:${port}`)
})
```
### routes/home.js
```
const express = require('express')
const router = express.Router()

router.get('/', (req,res)=>{
    res.send("Home page")
})

module.exports=router
```
### routes/about.js
```
const express = require('express')
const router = express.Router()

router.get('/login', (req,res)=>{
    res.send("Login page")
})

module.exports=router
```
### routes/login.js
```
const express = require('express')
const router = express.Router()

router.get('/about', (req,res)=>{
    res.send("About page")
})

module.exports=router
```