# Sweet-Libs

## Hacksplaining

### Cross-Site Request Forgery [(CSRF)](https://www.hacksplaining.com/exercises/csrf#)

Forging http request to trick users into triggering unintended actions

Any function that your users can perform deliberately is something they can be tricked into performing inadvertently using CSRF


attacks occur when a user is tricked into interacting with a page or script on a third-party site that generates a malicious request to your site.

Your server sees an http request from an authenticated user but the attacker can take control over the form data sent in the request

###HOW IT HAPPENS

Imagine you run a micro-blogging service that allows users to post their opinions in 140 character messages

A hacker notices that the posts on your site are created using get requests meaning that all of the information is available in the url of the http request

The hacker modifies the post creation url to include malicious data

The hacker gets one of the users email addresses and sends the user a tempting link pointing to the malicious url

The user clicks the link and your server interprets the request as the authenticated user writing post which is not what the user intended

The post is designed to draw other users in to click on it just like the first user

Now each user that clicks the link will post the link, opening more and more users to the hack

###PROTECTION

Protecting against CSRF requires two things:
ensuring that GET requests are side-effect free and ensuring that non-GET requests can only originate from your client-side code

If using Node, expresses csurf is a module that helps to protect against CSRF

Creating a a csurf middleware for CSRF token creation and validation adds a req.csrfToken() function to make a token which should be added to requests.  This token validates requests  against the users session or csrf cookie


###CODE EXAMPLE
```js
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
```js
<form action="/process" method="POST">
 <input type="hidden" name="_csrf" value="{{csrfToken}}">

 Favorite color: <input type="text" name="favoriteColor">
 <button type="submit">Submit</button>
</form>
```
### Cross-Site Scripting [(XSS)](https://www.hacksplaining.com/exercises/xss-stored#)
###HOW IT HAPPENS
Any app you make that allows for user input can be exploited by injecting malicious javascript if you do not properly protect against an attack. 

A hacker can enter malicious JavaScript through a script tag in a form in your app that could do something such as steal another users cookie which they could then use for session hacking. The attacker abuses an editable field by inserting some JavaScript code, which is evaluated in the browser when another user visits that page.

###PROTECTION

Unless your app is a content-management system, it is rare that you want your users to author raw HTML. Instead, you should escape all dynamic content coming from a data store, so the browser knows it is to be treated as the contents of HTML tags, as opposed to raw HTML. Most modern frameworks will escape dynamic content by default:


###CODE EXAMPLE

Rails templates escape HTML by default, so anything that looks like the following is generally safe:
```
<%= contents %>
```
