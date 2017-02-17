# Sweet-Libs

## Hacksplaining

### Cross-Site Scripting [(XSS)](https://www.hacksplaining.com/exercises/xss-stored#)

Any app you make that allows for user input can be exploited by injecting malicious javascript if you do not properly 


##CSRF

###Cross-site request forgery

Forging http request to trick users into triggering unintended actions

Any function that your users can perform deliberately is something they can be tricked into performing inadvertently using CSRF


attacks occur when a user is tricked into interacting with a page or script on a third-party site that generates a malicious request to your site.

Your server sees an http request from an authenticated user but the attacker can take control over the form data sent in the request

###HOW IT HAPPENS

Imagine you run twitter which is a micro-blogging service that allows users to post their opinions in 140 character messages

A hacker notices that the posts on your site are created using get requests meaning that all of the information is available in the url of the http request

The hacker modifies the post creation url to include malicious data

The hacker gets one of the users email addresses

The hacker sends the user a tempting link pointing to the malicious url

The user clicks the link

Your server interprets the request as the authenticated user writing post which is not what the user intended

The post is designed to draw other users in to click on it just like the first user

Now each user that clicks the link will post the link, opening more and more users to the hack

###PROTECTION

Protecting against CSRF requires two things:
ensuring that GET requests are side-effect free and ensuring that non-GET requests can only originate from your client-side code

If using Node, expresses csurf is a module that helps to protect against CSRF

Creating a a csurf middleware for CSRF token creation and validation adds a req.csrfToken() function to make a token which should be added to requests.  This token validates requests  against the users session or csrf cookie


###CODE EXAMPLE
```js```
var cookieParser = require('cookie-parser')
var csrf = require('csurf')
var bodyParser = require('body-parser')
var express = require('express')

// setup route middlewares
var csrfProtection = csrf({ cookie: true })
var parseForm = bodyParser.urlencoded({ extended: false })

// create express app
var app = express()

// parse cookies
// we need this because "cookie" is true in csrfProtection
app.use(cookieParser())

app.get('/form', csrfProtection, function (req, res) {
 // pass the csrfToken to the view
 res.render('send', { csrfToken: req.csrfToken() })
})

app.post('/process', parseForm, csrfProtection, function (req, res) {
 res.send('data is being processed')
})
```

###CODE IN THE VIEW
```js```
<form action="/process" method="POST">
 <input type="hidden" name="_csrf" value="{{csrfToken}}">

 Favorite color: <input type="text" name="favoriteColor">
 <button type="submit">Submit</button>
</form>
```
