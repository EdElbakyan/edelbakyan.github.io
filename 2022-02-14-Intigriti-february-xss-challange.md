---
title: "Intigriti february xss challange"
categories:
  - challanges
tags:
  - web
  - xss
  - intigriti
  - challange
---

## Intigriti february xss challange

The page presents a simple ui in which you are able to input text and get it back.


![[Pasted image 20220212222129.png]]

![[Pasted image 20220212222256.png]]

By inspecting the page source we can see that it gets two parameters q= and first= and checks if the length of q is less than 24 and if it's not it returns an error message.

![[Pasted image 20220212222447.png]]

Trying some xss payloads we can see that  ``` <style onload=alert(1)>```  works and alerts 1.

![[Pasted image 20220212222605.png]]

but as mentioned in challange we need to alert document.domain which in this case will exceed 24 characters.

I recognized that I was able to change the value of the "first" parameter in url and if I modify it and use it as part of my payload I may succedd.
The only way to use the "first" parameter is to evaluate the url. In that case the contents of the "first" parameter would be interpreted and executed as javascript and we may achieve xss.
```javascript
q=<style onload=eval(uri)>&first=alert(1)
```

When trying this payload I got an error in  the console and by looking at it i saw that it was because of https://... the ```//``` coming after ```https:``` was interpreted as a comment and because of that the other part of the payload wouldn't execute.

![[Pasted image 20220212223813.png]]

So i decided to use %0A which is the new line character and that way our payload would escape the comment and execute.

```javascript
?q=<style onload=eval(uri)>&first=%0Aalert(document.domain)
```

Finally I was able to get the payload working and alerting document.domain in both chrome and firefox.

POC
```url
https://challenge-0222.intigriti.io/challenge/xss.html?q=<style onload=eval(uri)>&first=%0Aalert(document.domain)
```