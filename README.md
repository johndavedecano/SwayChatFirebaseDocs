SwayChatFirebaseDocs
====================

##Schema

https://drive.google.com/file/d/0B9kV-qjAEAezaWhEaVQ3d2ZmUjA/view?usp=sharing

##Generating JWT Tokens
~~~
  // https://github.com/firebase/firebase-token-generator-php
  include_once "FirebaseToken.php";
  
  $data = [
    'uid' => 'some-uid',
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

##Logging In To Firebase
~~~
  var data = {
    email: $('#email').val(),
    name: $('#name').val(),
    init_message: $('#message').val()
  };

  var token = $.ajax({type: "GET", url: "http://localhost:8080", data: data, async: false}).responseText;
  
  var firebase = new Firebase('https://sweltering-heat-8664.firebaseio.com');
  
  firebase.auth(this.token, function(error, result) {
    if(error) {
      console.log(error);
    } else {
      console.log(result); // Returns the user information 
    }
  });
~~~
Read More: https://www.firebase.com/docs/web/api/firebase/auth.html

#Rules

Make the messages restricted from other users.

~~~
"messages" : {
    "$sessionID" : {
        ".read": "auth != null && root('sessions').child($sessionID).child('users').child(auth.uid).exists()",
        ".write":"auth != null && root('sessions').child($sessionID).child('users').child(auth.uid).exists() && data('from').val() == auth.uid",
        "$messageID" : {
          ".read": false,
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
| data('from').val() == auth.uid | Message from field must be equal to the uid of currently logged in user. |


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

#Prevent Infinite Loops

~~~
http://stackoverflow.com/questions/24830079/firebase-rate-limiting-in-security-rules/24841859#24841859
~~~

#Ways To Save Data

| Set           | Description                                                             | 
| ------------- |------------------------------------------------------------------------------| 
| set()         | Write or replace data to a defined path, like messages/users/<username> | 
| update()      | Update some of the keys for a defined path without replacing all of the data | 
| push()        | Add to a list of data in Firebase. Every time you call push() Firebase generates a unique ID, like messages/users/<unique-user-id>/<username> | 
| transaction() | Use our transactions feature when working with complex data that could be corrupted by concurrent updates | 


# Adding Message

~~~
var fb = new Firebase('https://<FIREBASE_LOCATION>/messages/'+ session_id);
fb.push({
  from    : "uid",
  name    : "Dave",
  message : "Test Message"
});
~~~







