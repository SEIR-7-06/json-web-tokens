# JSON Web tokens and YOU!

## So What Is It?


A web token (for use in Auth) is a compact, contained way to securely send information between a database and a user's computer as a JSON object. This information can be verified and trusted because it is digitally signed.

So why is this important? 

Once the user is logged in, each subsequent request will include the token, allowing the user to access routes, services, and resources that are permitted with that token. This way a customer does not have to manually reverify everytime they make a change.  

## Token Structure

In its simple form, a token is separated by 3 parts and will look as so

```
Header.Payload.Signature
```
Each section of the token is encoded by the base64url encoding algorithm for compression. 

Check out how that works [HERE](https://simplycalc.com/base64url-encode.php)

### Header

The header consists of two parts. The type (JWT) and Algorithm being used (such as HMAC, SHA256, or RSA)
 
```json
{
	"alg": "HS256",
	"typ":"JWT"
}
```

encoded it turns into...

```
ewoJImFsZyI6ICJIUzI1NiIsCgkidHlwIjoiSldUIgp9
```
	
### Payload
The payload is a container that holds the "claims" about the user. A "claim" is a dataset inside the token that holds information about the user and what the user can handle. There are 3 main types of claims.

- Registered claims
	* These are predefined claims that are not mandatory but recommended. Provides a set of useful claims about the user. 
	* I.E. expiration time to define how long until a user needs a new token.
- Public claims
	* These are openly defined claims that you can make yourself. It is a good idea to stick to the standards set by the [Token Regitry](https://www.iana.org/assignments/jwt/jwt.xhtml).
- Private claims	
	* These are custom claims created to share information between parties that agree on using them and are not already a registered claim or a public claim. 

So it will look similar to
	
```json
{
	"sub":"12345678",
	"name": "Bobbo",
	"admin": true,
}
	
```

encoded it looks like...

```
ewoJInN1YiI6IjEyMzQ1Njc4IiwKCSJuYW1lIjogIkJvYmJvIiwKCSJhZG1pbiI6IHRydWUKfQ
```

### Signature

Now, this is where the magic comes in. To creat the signature you take the encoded header, the encoded payload, a secret, and the algorithm specified in the header and sign that. 

when you encode that it looks something like this..

```
4pcPyMD09o1PSyXCjTwXyr4BsezdI1AVTmud2fU4

```

This is to verify that the message was not changed along the way to either the user or back the database. 

### Lets put it all together now

So when you combine all three you get this looooooooooooong 3 point string. That is what is passed back and forth between both database and client. 

```
ewoJImFsZyI6ICJIUzI1NiIsCgkidHlwIjoiSldUIgp9.ewoJInN1YiI6IjEyMzQ1Njc4IiwKCSJuYW1lIjogIkJvYmJvIiwKCSJhZG1pbiI6IHRydWUKfQ.4pcPyMD09o1PSyXCjTwXyr4BsezdI1AVTmud2fU4
```

## How do they work?

In Auth, a web token is returned when a user successfully "logs in" using their credentials. So upon login, the token is sent to the client from the database and stored in local storage. 

From there anytime the client wants to be **Authorized** it will send the Token to the database that will then check the Token for its **Authorization** and then allow the client to access the correct routes.

One way to understand this is by thinking of an arcade machine. When you go to an arcade and want to play a game you must insert a token to get started. Once started you have been **Authorized** to play the game. 

![arcade gif](https://media.giphy.com/media/o2su0spy4u2zK/giphy.gif)

Now as you are playing if you were to die in the game then you would have to insert more tokens *within the time limit* to continue your **Authorization** and continue the same character. If you do not then you will not be **Authorized** anymore to play. 

### How do they get sent?

A user sends the token along with their request to access the page. Inside of the request, object is the header object that will hold the **Authorization** header. The **Authorization** header looks as so..

```
Authorization: Bearer <ridiculous token string>
```

From here the token is grabbed and then cross-referenced with the token data held for the user in the database. If the information is valid then the user is **Authorized**.

Now that is a lot to take in. So let's look at the documentation flowchart to get a better understanding. 

![auth flowchart](https://cdn2.auth0.com/docs/media/articles/api-auth/client-credentials-grant.png)

*image provided by jwt documentation*

In the next lesson we will tackle using these in a Node.js environment.
