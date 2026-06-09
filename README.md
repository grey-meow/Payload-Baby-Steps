# Payload-Baby-Steps
Step-by-step for hacker babies to design and diy their own payloads. Parental monitoring is advised 
## DIY XSS Payload
1. Identify the context
   Like where do you wat input to land in HTML code
   For HTML there's the in between html tags, inside a HTML attribute <input type="text" value = "USER_INPUT">
   Then theres the jvascript kid/ where the input is directly inside an existing script block. let name = 'USER_INPUT':

2. Boundary Escaping.
   now a payload supposed to be breaking out of the intended boundary. by boundary means how website supposedly take in texts. not executing codes from inside the search bar.
Let's say ur in HTML body, start a new tag. like this
"
<script>alert(1)</script>
"
if inside an attribute. close attribute, sometimes the tag with " or ' and close >
"><script>alert(1)</script>
alternative:
stay inside tag but add event handler like so
" autofocus onfocus="alert(1)
3. This is not the 80s. it gets hard kid. Now you gotta bypass things. Larping VIP pass to these things to get it work. Well wehave this thing called filter handling and WAFs bypassing. 
<script> is wayyyyy to obvious. It's like telling your mom that you didn't sneak out to the playground with mud inm your shoes . We gotta be creative about it.
In this case, we can use alternative tags. there are several:
<img src=x onerror=alert(1)> or <svg onload=alert(1)
or be like one of those kids that wants to use the same name with 100 other people on Instagram
Let's say your name is Catherine, we can do c4th3r1n3.
Here's what it looks like if we apply that to our payload craft:
<sCrIpt>alert(1)</ScRiPt>
you can also play around like this:
<scr<scipt>ipt>

## DIY SQL Payload
1. Understand Backend Query
   so sometimes a login or a search bar of a website (or even search engine) it features a running backend query that looks like this
   SELECT * FROM users WHERE username = 'USER_INPUT' AND password = 'PASSWORD';
   This is typical sql command 
2.Break the SQL syntax
  To break your ex's heart, you have to first manipulate him or her or whatever pronouns there are.
First break out of data string literal. It is almost. almost always done using a single quote like so (').
if you tryna use (') as the username. query breaks and it will throw sntax error because quotes are unbalanced:
SELECT * FROM users WHERE username = '' ' AND password = 'PASSWORD';
--                                    ^ Syntax Error Here
3. Bypass
   This is where injecting happens.
   We now gotta do:
   error --> successful exploit
   so here's how:
   * inject valid SQL logic, turn error to TRUE.
   * comment rest of query so it doesnt cause error.
   * example payload ' OR 1=1 --
   * How database sees it:
   * SELECT * FROM users WHERE username = '' OR 1=1 -- ' AND password = 'PASSWORD';
   * note: OR 1=1 condition is always true.
   * (--) or (#) tells database that everything it is a comment, effectively deleting password check entirely.
  4. 
