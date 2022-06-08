---
layout: post
title:  "A bug I cannot explain or solve"
date:   2022-02-08 10:10:15 +0700
tags: [bash, bug]
---

# A bug I cannot explain or solve.

Last week I came across some weird behavior. I’m not sure if it can be considered a bug or not because I cannot grasp my head around it.
I needed to automate some API calls, and I did that in a bash script. There were two different APIs that I would call. First I would call `API A` to get a `JWT` token. I would then call `API B` including this token in the `Authorization Bearer <token>` header. I’ve done this manually (using both curl and postman) multiple times earlier without any issues.
My bash script looked something like this:

```bash
#!/bin/bash
...
token=$(curl -X POST https://apiA.com/token -H "Content-Type: application/json")
token=$(echo token | jq ".token" | sed -e 's/^"//' -e 's/"$//' -e 's/\n//' -e 's/\r//')
...

repsonse=$(curl -X PUT https://apiB.com/endpoint -H "Authorization Bearer $token" --data-raw "some-json-data")
echo $response
```

The problem was related to the authentication of the token. The token was a JWT token and, let's say that it looked like this: `xxx`. The above script resulted in that I got access denied from `API B`. It was time for debugging.

- I decoded the token and verified that it looked valid. It contained the correct content, and it was **not** expired.
- I tried using the same token in postman for calling `API B`, but I still got access denied.
- I tried to generate a token in postman and use that token instead. That worked, and I got 200 back! The problem is now confirmed to be related to the token fetching in the script.

```bash
#!/bin/bash
...
# token=$(curl -X POST https://apiA.com/token -H "Content-Type: application/json")
# token=$(echo token | jq ".token" | sed -e 's/^"//' -e 's/"$//' -e 's/\n//' -e 's/\r//')

token="<token-from-postman>"
repsonse=$(curl -X PUT https://apiB.com/endpoint -H "Authorization Bearer $token" --data-raw "some-json-data")
echo $response
```

- I verified that the length of the token was always the same `echo -n "$token" | wc -c`. It  was always 2017 chars.
- I wrote the token to a file and then read it from the file. I still did not succeed.
- I took a hex dump of the token and saw that it had `a0` at the end, meaning that the token contained some weird `LF` (line feed) at the very end. The above sed commands didn’t remove the last LF, and I started to think that this could be the reason why it fails.
- I checked the line-endings character, and it was `$`, which should be correct.
- For some reason, after trying everything else, I appended a `$` to the token generated from the script `xxx$` and then used that one in postman... And it worked! I tried adding multiple extra chars, for instance, `xxx$$$` , `xxx===` and `xxx$=$` . They all worked!
- I now took the token generated from the script with the extra characters at the and used it in the `PUT` to `API B` directly, and it also worked!
- I now thought that I had a solution to the problem. Just append some amounts of `$`to the token, and it will work. I added the following line before the call to API B `token="${token}\$\$\$"` as a work-around, but it did **not** work!
- I copied the new token from the script `xxx$$$` and used it in postman. I thought it was going to work, for sure. But, It did **not** work! I removed the last `$` to get a token like  `xxx$$`, and called `API B` using postman with the modified token, and now it worked! I did not understand anything. The only pattern I could find was that the token copied from the script would never work. I had to modify it somewhat to get the token to work even using postman. Adding or removing padding characters like `$=?` would often solve the problem. I gave up. It did not make any sense.

The next day I rewrote the whole thing in python, and it worked from the start. I have still not figured out the problem. I assume it is either some issues with the token padding, the LF, or the validation of the token on the backend. Or my bash knowledge.