SwayChatFirebaseDocs
====================
##Table Of Contents
1. Introduction
2. Schema
3. Generating Tokens
4. Authentication
5. Messaging
6. Security

##Introduction
We will use Firebase as our socket and database server. Firebase is a realtime backend as a service that allows you to create incredible apps. Save, Store and Update Data in realtime directly from the browser or other devices.
##Schema

https://drive.google.com/file/d/0B9kV-qjAEAezaWhEaVQ3d2ZmUjA/view?usp=sharing

##Generating JWT Tokens
By default, anyone can actually access Firebase anonymously however but for security, we have to set some rules to protect our data from being written, deleted and updated by anyone so we have to make some kind of authentication.

Firebase has different ways of authentication service providers. There are the following

| Provider      | Description        | 
| ------------- | ------------- |
| Custom      | Generate your own login tokens. Use this to integrate with existing authentication systems. You can also use this to authenticate server-side workers. |
| Email & Password      | Let Firebase manage passwords for you. Register and authenticate users by email & password.      | 
| Anonymous | Build user-centric functionality without requiring users to share their personal information. Anonymous authentication generates a unique identifier for each user that lasts as long as their session. |
| Facebook | Authenticate users with Facebook by writing only client-side code. |
| Twitter | Authenticate users with Twitter by writing only client-side code. |
| GitHub | Authenticate users with GitHub by writing only client-side code. |
| Google | Authenticate users with Google by writing only client-side code. |

In this case well use "Custom" authentication so that well have more control on the information that we are going to get from our users. 

As per example, we have to create some kind of api in our server to where we can send user information to convert it and generate a JWT token. This token well be returned to the client and client will use this to login to Firebase.

~~~
  // https://github.com/firebase/firebase-token-generator-php
  include_once "FirebaseToken.php";
  
  $data = [
    'uid' => 'some-uid', // UID Generate new UID()
    'name'         => 'FooBar',
    'email'        => 'FooBar',
    'ip_address'   => 'FooBar',
    'user_type'    => 'FooBar',
    'init_message' => 'FooBar',
    'domain'       => 'FooBar',
    'status'       => 'FooBar',
    'created_at'   => date("Y-m-d H:i:s"),
    'updated_at'   => date("Y-m-d H:i:s")
  ];

  $tokenGen = new Services_FirebaseTokenGenerator("<YOUR_FIREBASE_SECRET>");
  
  header('content-type: application/json; charset=utf-8');
  header("access-control-allow-origin: *");
  $token = $tokenGen->createToken($data);
  echo $token;
~~~
##Authentication
Given, we already have a valid JWT token, in our client, we have to create a new instance of firebase object in which we pass our Firebase URL as its argument.

We will then use firebase.auth(token, callback(error,user)); method to login to our Firebase. This method gets the token as first argument and the callback or anonymous function as second argument. 

###Callback Arguments
1. error - **null** if success and **error object** if theres a problem
2. user - Auth object which consists mainly of uid and other information passed to the JWT token generator.
~~~
  var data = {
    email: $('#email').val(),
    name: $('#name').val(),
    init_message: $('#message').val()
  };

  var token = $.ajax({type: "GET", url: "http://localhost:8080", data: data, async: false}).responseText;
  
  var firebase = new Firebase('https://sweltering-heat-8664.firebaseio.com');
  
  firebase.auth(token, function(error, result) {
    if(error) {
      console.log(error);
    } else {
      console.log(result); // Returns the user information 
    }
  });
~~~
Read More: https://www.firebase.com/docs/web/api/firebase/auth.html

##Security

###Rules

Make the messages restricted from other users.

~~~
"messages" : {
    "$sessionID" : {
        ".read": "auth != null && root('sessions').child($sessionID).child('users').child(auth.uid).exists()",
        ".write":"auth != null && root('sessions').child($sessionID).child('users').child(auth.uid).exists() && newData('from').val() == auth.uid && newData('created_at').val() == now",
        "$messageID" : {
          ".read": "auth != null && root('sessions').child($sessionID).child('users').child(auth.uid).exists()",
          ".write":false, 
          ".validate": "newData.hasChildren(['from', 'name', 'message'])"
        }
    }
}

// root('sessions').child($sessionID).child('users').child(auth.uid);
// sessions/<SESSION_ID>/users/<USER.UID>.json
~~~

| Rule          | Description   | 
| ------------- |---------------| 
| auth != null      |  User must be logged in |
| root('sessions').child($sessionID).child('users').child(auth.uid).exists()| Check if the user belongs to the session |
| newData('from').val() == auth.uid | Message from field must be equal to the uid of currently logged in user. |
| newData('created_at') == now | Checks if created at field which is a timestamp is equal to firebase current timestamp |


Only admin can write to the sessions and users(operators or visitors) collections
~~~
"sessions" : {
    ".read": false,
    ".write":false
    "$sessionID" : {
        ".read": "auth != null && data('users').child(auth.uid).exists()"
    }
}

"operators" : {
    ".read": false,
    ".write":false
    "$userID" : {
        // Operator can only read his own information
        ".read": "auth != null && root('operators').child($userID).exists() && $userID == auth.uid"
    }
}
~~~

###Prevent Infinite Loops

~~~
http://stackoverflow.com/questions/24830079/firebase-rate-limiting-in-security-rules/24841859#24841859
~~~






