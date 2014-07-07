# testProject
### Getting Started with Sails on OSX

Sails.js has a wonderful [video intro](https://www.youtube.com/watch?v=GK-tFvpIR7c) but the syntax is different for the 0.9 release.  Updated steps below.

```
brew install node
npm -g sails
sails new testProject --linker
cd testProject
sails lift
```
in browser, go to: http://localhost:1337/
the sails app is running with some references to what to do next to generate an JSON API and links to docs


```
sails generate user
```

Now in the browser
```
http://localhost:1337/user/create?name=Tim
http://localhost:1337/user/create?name=Judy
http://localhost:1337/user/create?name=Lee
http://localhost:1337/user/create?name=Maggie
```

creates users 
in browser, go to: http://localhost:1337/user/
```
[
  {
    "name": "Tim",
    "createdAt": "2014-07-07T12:43:35.723Z",
    "updatedAt": "2014-07-07T12:43:35.723Z",
    "id": 1
  },
  {
    "name": "Judy",
    "createdAt": "2014-07-07T12:48:12.189Z",
    "updatedAt": "2014-07-07T12:48:12.189Z",
    "id": 2
  },
  {
    "name": "Lee",
    "createdAt": "2014-07-07T12:48:15.247Z",
    "updatedAt": "2014-07-07T12:48:15.247Z",
    "id": 3
  },
  {
    "name": "Maggie",
    "createdAt": "2014-07-07T12:48:19.187Z",
    "updatedAt": "2014-07-07T12:48:19.187Z",
    "id": 4
  }
]
```

can create attributes, like this
http://localhost:1337/user/update/1?email=tim@whatever.com
```
{
  "name": "Tim",
  "createdAt": "2014-07-07T12:43:35.723Z",
  "updatedAt": "2014-07-07T12:52:48.464Z",
  "id": 1,
  "email": "tim@whatever.com"
}

```

using POST 

```
curl -X POST -d "{}" -H "Content-Type: application/jon" http://localhost:1337/user/find
```
returns all the user, just like http://localhost:1337/user in the browser, but now I can adjust what I'm looking for

```
curl -X POST -d '{"limit":2}' -H "Content-Type: application/jon" http://localhost:1337/user/find
```
returns
```
[
  {
    "name": "Tim",
    "createdAt": "2014-07-07T12:43:35.723Z",
    "updatedAt": "2014-07-07T12:52:48.464Z",
    "id": 1,
    "email": "tim@whatever.com"
  },
  {
    "name": "Judy",
    "createdAt": "2014-07-07T12:48:12.189Z",
    "updatedAt": "2014-07-07T12:48:12.189Z",
    "id": 2
  }
]
```
or in the browser: http://localhost:1337/user?limit=2


with sorting
```
curl -X POST -d '{"limit":2, "sort":"name ASC"}' -H "Content-Type: application/jon" http://localhost:1337/user/find
```

other options
```
{
  "where": {
  	"name": {
    	"contains":"e"	
    }
  }
}


{
  "where": {
  	"name": {
    	"startsWith":"L"	
    }
  }
}


```

## Check out sockets
Open Javascript console in the browser, and you will see
```
Connecting to Sails.js... app.js:60
Socket is now connected and globally accessible as `socket`.
e.g. to send a GET request to Sails, try 
`socket.get("/", function (response) { console.log(response); })` 
```
Take a look at assets/linker/js/app.js where you will see the code that creates that output

Try this at the console:
```
socket.get('/user', {}, function (users) {console.log(users)})
```
and you will see your list of users

sails maps the socket io requests to express requests automatically


in another browser window:
```
http://localhost:1337/user/create?name=Fred
```

then see this
```
New comet message received ::  
Object {model: "user", verb: "create", data: Object, id: 5}
```

expand the object to see the data for that object is available

## Lock it down

in config/policies.js
```
module.exports.policies = {

  // Default policy for all controllers and actions
  // (`true` allows public access) 
  '*': true
```
indicates that anyone can ask the server for anything

we'll change what is accessible on the user model
```
module.exports.policies = {

  // Default policy for all controllers and actions
  // (`true` allows public access) 
  '*': true,

  'user': {
    '*': 'authenticated',
    'findAll': true,
    'find':true
  }
```

This refers to an 'isAuthenticated' policy that is defined already in api/policies/isAuthenticated.js  (this is actually just express middleware)

then if you go to http://localhost:1337/user that works

but if you try to create a user:
```
http://localhost:1337/user/create?name=Mary
```
you get a 403 forbidden










## Questions

* Where's the data?

By default, Sails uses simple disk storage for development only.  You can see this in 'config/adapters.js'

```
module.exports.adapters = {

  // If you leave the adapter config unspecified 
  // in a model definition, 'default' will be used.
  'default': 'disk',

  // Persistent adapter for DEVELOPMENT ONLY
  // (data is preserved when the server shuts down)
  disk: {
    module: 'sails-disk'
  },


```

