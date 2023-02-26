# Admin dashboard

## Information
**Category** | **Points** 
--- | --- 
Web | 100

## Description

I built my first website, admin dashboard with bootstrap and PHP!
Feel free to try it! Hope there is no bug..

## Solution

Analyzing the source given, i found an endpoint /addadmin.php that we need to have role admin with its crsf token.

Another endpoint is in /report.php, which create csrf-token correspond to user-session and the token will change after 30 seconds or users reenter the endpoint. When we send an url , a bot will visit that url. 
Therefore, the goal is to make the bot add an fake account with role admin by feeding payload entering addadmin.php.

Specifically , the payload is a get request as the form in the source get data via $_REQUEST, so both post or get method will work. 
```
http://<challenge-server>/addadmin.php?username=Onirique&password=123&csrf-token=<admin_token>
```

The csrf-token is create via linear congruential generator (LCG), with known modulo $M$ and unknown multiplier $A$ and increment $C$
```
X0 = [username]
X1 = (A * X0 + C) [mod M]
X2 = (A * X1 + C) [mod M]
```
We can easily compute A,C using these two equations by getting my own X0,X1,X2:
```
C = (X1*X1-X2*X0)*(X1-X0)^-1 [mod M]
A = (X1-C)*(X0^-1) [mod M]
```

Finally, we can generate csrf-token for admin by compute X1 with known X0 and LCG parameters. As i computed all these by hands, there is no script in this wu :)).
> Flag is: ACSC{C$rF_15_3VerYwh3Re!}

