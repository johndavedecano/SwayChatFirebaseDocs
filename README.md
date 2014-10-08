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
      console.log(result);
    }
  });
~~~

#Rules
















