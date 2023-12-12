---
marp: true
footer: CS571 Building User Interfaces | Cole Nelson & Yuhang Zhao | Lecture 26: Building Secure UIs
paginate: true
theme: uncover
style: |
  section {
    text-align: left
  }
  .center-info {
    text-align: center
  }
---


# **Building Secure UIs**
### CS571: Building User Interfaces

###

#### Cole Nelson & Yuhang Zhao

---

### Announcements


 - Please complete your course evaluations! :)

 - All HWs **must** be turned in by tomorrow evening.

 - Office hours end Friday evening, by appointment only following.

 - Today's content will *only* be on the Bonus Quiz.
   - "Due" on 12/13, can be completed until 12/18.


---

### Final Exam

Final Exam is on Monday, December 18th at 10:05 am.

<div>

   - SEC001 in Bascom 272
   - SEC002 in Humanities 3650

</div>

You will have 90 minutes for 40 MC (20 on design and 20 on implementation)

   - Alternative final exam confirmation and information has been sent out. Fill out form by **EOD TODAY**.

Don't forget your double-sided notesheet!

---

### What will we learn today?

<div>

 - How to (not) end up in jail?
 - What are bug bounty programs?
 - What are the OWASP Top 10?
 - How do SQLi attacks work?
 - How do XSS attacks work?
 - How do vulnerable dependencies create risk?
 - Why is client-side validation insufficient?
 - What are common mitigation strategies?

</div>

---

### A Disclaimer

*a badger wearing a red shirt with a w on it in jail for committing cybercrimes, pixel art*

###

generated using [DALL-E](https://openai.com/product/dall-e-2)

![bg right 90%](figures/badgerjail.png)

---

![bg 90%](figures/html.png)

---

![bg 90%](figures/h1.png)
![bg 90%](figures/obb.png)
![bg 90%](figures/bc.png)

---

### Building Secure User Interfaces

Make sure you have permission to resources before performing security audits!

###

<div>

 - [HackerOne](https://www.hackerone.com/)
 - [OpenBugBounty](https://www.openbugbounty.org/)
 - [BugCrowd](https://www.bugcrowd.com/)

</div>

###

Look for **safe harbors** or **local resources**.

---

![bg](figures/top10.png)

---

<div>

### OWASP Top 10 (2021)

1. Broken Access Control
2. Cryptographic Failures
3. Injection
4. Insecure Design
5. Security Misconfiguration

</div>


---

### OWASP Top 10 (2021)

<div>

6. Vulnerable and Outdated Components
7. Identification and Authentication Failures
8. Software and Data Integrity Failures
9. Security Logging and Monitoring Failures
10. Server-Side Request Forgery

</div>

---

### Common Vulnerabilities

Vulnerabilities affect both frontends *and* backends!


Today we will look at...

<div>

 - SQL Injection
 - Cross-Site Scripting (XSS)
 - Vulnerable and Outdated Components
 - Software and Data Integrity Failures 

</div>

---

# SQL Injection (SQLi)

`' OR 1=1; DROP TABLE grades; --`

---

# SQL Injection (SQLi)

A SQL injection attack consists of insertion or “injection” of a SQL query via the input data from the client to the application. A successful SQL injection exploit can read sensitive data from the database, modify database data...

[OWASP Definition](https://owasp.org/www-community/attacks/SQL_Injection)

---

### SQLi

Imagine we have the following SQL query...

```jsx
INSERT INTO Messages(title, content) VALUES('${title}', '${content}')
```

When the `title` is "hello" and `content` is world...

```jsx
INSERT INTO Messages(title, content) VALUES('hello', 'world')
```

All is good!


---

### SQLi

Imagine we have the following SQL query...

```jsx
INSERT INTO Messages(title, content) VALUES('${title}', '${content}')
```

When the `title` is `',''); DROP TABLE Messages; --` and the `content` is anything else...

```jsx
INSERT INTO BadgerMessage(title, content) VALUES('',''); DROP TABLE Messages; --', 'anything')
```

All is **NOT** good!

---

### SQLi Mitigations

Sanitize your inputs **on the backend!**

 - Frontend sanitization is *not* sufficient!

Use parameterized queries instead!

![bg right 90%](figures/sanitize.webp)


<sub><sup>[Image Source](https://www.fishersci.com/shop/products/purell-advanced-hand-sanitizer-gel-6/p-7159163)</sup></sub>


---

# SQLi Demo
CS220 | [Download here!](https://github.com/CS571-F23/week15-sui-example) ([requires anaconda](https://www.anaconda.com/download))

---

# Cross-Site Scripting (XSS)
`<img src="0" onerror="alert(document.cookie)"/>`


---

### Cross-Site Scripting (XSS)

Cross-Site Scripting (XSS) attacks are a type of injection, in which malicious scripts are injected into otherwise benign and trusted websites. XSS attacks occur when an attacker uses a web application to send malicious code, generally in the form of a browser side script, to a different end user. 

[OWASP Definition](https://owasp.org/www-community/attacks/xss/)


---

### HW2 XSS

Render each student using `innerHtml`

```jsx
let html = "<div>";
html += `<h2>${student.name}</h2>`;
html += "</div>";
return html;
```


---

### HW2 XSS

When input is `Michael`...

```html
<div>
  <h2>Michael</h2>
</div>
```

`<i>Michael</i>`...

```html
<div>
  <h2><i>Michael<i></h2>
</div>
```

---

### HW2 XSS

`<script>alert("oops!")\</script>`...

```html
<div>
  <h2><script>alert("oops!")</script></h2>
</div>
```

`<img src="0" onerror="alert(document.cookie)"/>`...

```html
<div>
  <h2><img src="0" onerror="alert(document.cookie)"/></h2>
</div>
```

--- 

### DOM-based vs Persistent XSS

DOM-based XSS
```
https://example.com/search?q=%3Cimg%20src=%220%22%20onerror=%22alert(1)%22/%3E
```

Persistent XSS

![w:800](figures/bascom.png)

---

### XSS Mitigations

Sanitize your displays!

Do not create a sanitizer yourself!

![bg right 90%](figures/sanitize.webp)

React [performs sanitization](https://legacy.reactjs.org/docs/dom-elements.html#dangerouslysetinnerhtml) for you.



<sub><sup>[Image Source](https://www.fishersci.com/shop/products/purell-advanced-hand-sanitizer-gel-6/p-7159163)</sup></sub>

---

# XSS Demo w/ BadgerChat
BadgerChat is *NOT* a safe harbor -- please ask for permission before pentesting.

---

![bg 90%](figures/samy.png)

---

# SQLi & XSS Demos 
Using OWASP JuiceShop | [Download here!](https://owasp.org/www-project-juice-shop/)

---

# Use of Outdated and Vulnerable Components
`angular@1.8.3` among many, many others...


---

![w:1000](figures/npmi.png)

---

![w:1000](figures/npmaudit.png)

---

![w:1000](figures/npmls.png)

---


### Outdated and Vulnerable Components

A vulnerable dependency does not *necessarily* mean the application is vulnerable.

Likewise, an application without vulnerable dependencies *could still* be vulnerable.

Vulnerable dependencies are *flags* to look into.

---

### Checking Vulnerabilities

Check the CVE (Common Vulnerabilities and Exposures)

e.g. CVE-2021-3803...

<div>

 - [NIST](https://nvd.nist.gov/vuln/detail/CVE-2021-3803)
 - [GitHub](https://github.com/advisories/GHSA-rp65-9cf3-cjxr)
 - [Snyk](https://security.snyk.io/vuln/SNYK-JS-NTHCHECK-1586032)

</div>

###

Does it have to be in the production build? Can it be specified as a [dev dependency](https://docs.npmjs.com/specifying-dependencies-and-devdependencies-in-a-package-json-file)?

---

![bg 90%](figures/log4j.png)

---

![bg 90%](figures/ang.png)

---

### Outdated Dependency Mitigations

Keep your dependencies up-to-date!

 - e.g. [create-react-app](https://create-react-app.dev/) is no longer  maintained!

<div>

 - Static Analysis (SAST) Tools
   - [OWASP DependencyCheck](https://owasp.org/www-project-dependency-check/)
 - Continuous Maintenance
 - Minimize Surface Area

</div>

###

Beware of changing technologies!

---

# Software and Data Integrity Failures

---

### Validation

Frontends are just a way of getting to the backend!

**Do not rely solely on frontend validation.**

The user can send more than you allow them to.

![bg right 90%](figures/frontvalid.png)


---

![bg 70%](figures/validation.jpg)

<br/><br/><br/><br/><br/><br/><br/><br/><br/>


<sub><sup>[Image Source](https://www.reddit.com/r/ProgrammerHumor/comments/5gvawb/client_side_validation/)</sup></sub>

---

# Software and Data Integrity Failure Demo

On [OWASP JuiceShop](https://owasp.org/www-project-juice-shop/)

---

# Bonus Quiz

<div>

 - Two multiple-choice questions on today's content.
 - A [capture-the-flag](https://en.wikipedia.org/wiki/Capture_the_flag_(cybersecurity)) on [Badger Backers](https://www.cs571.org/f23/bonus)
   - This is the **ONLY** API we allow you to "hack"
   - **Hint:** Its a "Software and Data Integrity Failure"

</div>

---

# The Flag

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

You'll paste this in the last quiz question.

![bg 90%](figures/broken_api.png)

---

# What can we do in defense?

---

### Mitigation Strategies

Technical strategies may include...

<div>

 - Obfuscation
   - e.g. don't produce [a sourcemap](https://vitejs.dev/config/build-options#build-sourcemap)
 - Web Application Firewall (WAF)
 - Containerization (Using [Docker](https://www.docker.com/) or [VMs](https://www.virtualbox.org/))
 - Defense in Depth (Swiss Cheese Approach)

</div>

---

### Mitigation Strategies

Non-technical strategies may include...

<div>

 - Threat Modeling
 - Least Privilege
 - Scary Messages
 - Ask Nicely :)

</div>

---

# \</CS571>

What's next?

**CS570** Introduction to Human-Computer Interaction 
   → explore UX methods (research, design, evaluation)

###

**CS770** Human-Computer Interaction 
→ core topics and research methods in HCI research


See also [hci.cs.wisc.edu](https://hci.cs.wisc.edu/).


---

# Thank you! :)