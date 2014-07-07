# testProject
### a Sails application

```
brew install node
npm -g sails
sails new testProject --linker
cd testProject 
sails lift
```

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

