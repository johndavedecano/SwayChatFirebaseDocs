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
  $token = $tokenGen->createToken($data);
~~~

















