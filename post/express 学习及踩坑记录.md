### 资料

https://expressjs.com/en/api.html

### 特殊情况

``` JS
const express = require('express')
const app = express()

app.use((req, res, next)=>{
  console.log(req.baseUrl, req.originalUrl, req.path, req.url, req.params)
  next()
})

// req.path
app.get(['/ex', '/ex/', '/ex/*'], (req, res)=>{
  console.log(req.path)
  res.end('path: ' + req.path)
})
// https://github.com/expressjs/express/issues/3508
// strict Enable strict routing.  Disabled by default, “/foo” and “/foo/” are treated the same by the router.

// req.params
app.get(['/xp/', '/exp/:exs/*', '/exp/:exs*'], (req, res)=>{
  console.log(req.path, req.url, req.params)
  res.end(req.params)
})
// NOTE: Express automatically decodes the values in req.params (using decodeURIComponent).

// 测试 request /e/d/s/v/
app.get('/e/*', (req, res, next)=>{
  console.log(req.originalUrl, req.url, req.baseUrl, req.path, req.params)
  next()
})
app.get('/e/:i*', (req, res, next)=>{
  console.log(req.originalUrl, req.url, req.baseUrl, req.path, req.params)
  next()
})
app.use('/e/:i*', (req, res, next)=>{
  console.log(req.originalUrl, req.url, req.baseUrl, req.path, req.params)
  next()
})
app.get('/e/:i/*', (req, res)=>{
  console.log(req.originalUrl, req.url, req.baseUrl, req.path, req.params)
  res.end(req.params[0])
})
app.use((req, res)=>res.end('no match ' + req.originalUrl))
app.listen(3000)
```