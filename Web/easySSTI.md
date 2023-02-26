# easySSTI

## Information
**Category** | **Points** 
--- | --- 
Web | 200 

## Description

Can you SSTI me?

## Solution

Based on this [article](https://www.onsecurity.io/blog/go-ssti-method-research/), I understand that I can call any function just like how we exploit SSTI via Python.

Reading the source, I found that in the middleware, it retrieves the Template header, converts it into a byte array buffer, and sets the "template" variable for the context. Then, it gets the "template" variable and returns it to the client if the template is not empty. Now, I can craft a simple payload to retrieve the flag file: {{.File "/flag"}}.

However, I also need to bypass the WAF, since it blocks the word "ACSC". So, I need to find a way to encode or cut the string. And i came up with this payload:
```
{{(.Echo.Filesystem.Open ("../Flag")).Read (.Get "template")}} {{slice (.Get "template") 1}}
```
![alt text](https://github.com/junvalentine/ACSC_Writeup/blob/main/Web/web_image.png)

Convert those number to Ascii character to get the flag.

> Flag is: ACSC{h0w_did_y0u_leak_me}

