# Postbook CTF Challenge
**Difficulty:** Easy  
**Category:** Web  
**Flags:** 7/7

---

## 🧠 Overview
Postbook is a web application CTF challenge that tests various web security concepts including authentication bypass, parameter manipulation, cookie tampering, and authorization flaws. The challenge involves finding hidden flags through different exploitation techniques.

---

## 🔍 Reconnaissance
When first accessing the website, I performed initial reconnaissance:
- Examined the web application structure
- Looked for potential injection points in forms
- Tested URL parameters for manipulation
- Analyzed authentication mechanisms
- Investigated cookie handling

---

## 🚩 Step 1: First Flag - URL Parameter Manipulation
I started by exploring the website structure and testing different tactics from previous challenges. The key breakthrough came when I tested URL parameter manipulation to access hidden content.

**Discovery:**
I tried exploiting the URL structure to see if different IDs would reveal hidden content:
```
URL: https://27d19fabe4b0253088a79f0fd442b306.ctf.hacker101.com/index.php?page=view.php&id=2
```

This allowed me to view a hidden comment and revealed the **first flag**.

---

## 🔐 Step 2: Second Flag - Password Brute Force Attack
The hint mentioned that the user had an easy password, which suggested a brute force attack approach.

**Tools Used:**
- Hydra for password brute forcing
- RockYou wordlist for common passwords

**Command:**
```bash
hydra -l user -P /usr/share/wordlists/rockyou.txt \
0086785f04db9d6b04f502f161e248a4.ctf.hacker101.com \
https-post-form "/index.php?page=sign_in.php:username=^USER^&password=^PASS^:wrong username/password"
```

**Results:**
```
[443][http-post-form] host: 0086785f04db9d6b04f502f161e248a4.ctf.hacker101.com   
login: user   password: password
1 of 1 target successfully completed, 1 valid password found
```

Successfully cracked the credentials: `user:password` and obtained the **second flag**.

---

## 📝 Step 3: Third Flag - Form Parameter Manipulation
**Hint:** "You should definitely use 'Inspect Element' on the form when creating a new post"

**Method:**
1. Inspected the form elements when creating a new post
2. Used Burp Suite to intercept and modify form requests
3. Changed the ID parameter in the form submission
4. Modified the ID to `9` when editing a form

This manipulation revealed the **third flag**.

---

## 🔢 Step 4: Fourth Flag - Mathematical Hint Decoding
**Hint:** "189 * 5"

**Analysis:**
- Calculated: 189 × 5 = 945
- Realized this might correspond to an ID parameter
- Tested the calculated value as an ID in the URL

**Solution:**
```
/index.php?page=view.php&id=945
```

Accessing this URL while not logged in revealed the **fourth flag**.

---

## ✏️ Step 5: Fifth Flag - Unauthorized Post Editing
**Hint:** "You can edit your own posts, what about someone else's?"

**Method:**
1. Attempted to edit another user's post through the normal interface
2. Used Burp Suite to intercept the edit request
3. Modified the "Hello World" post by adding the character "d"
4. Successfully edited another user's content

The successful unauthorized edit revealed the **fifth flag**.

---

## 🍪 Step 6: Sixth Flag - Cookie Manipulation
**Hint:** "The cookie allows you to stay signed in. Can you figure out how they work so you can sign in to user with ID 1?"

**Discovery:**
The authentication cookies were encoded using MD5 hashing:
- User ID 1: `c4ca4238a0b923820dcc509a6f75849b`
- User ID 2: `c81e728d9d4c2f636f067f89cc14862c`

**Method:**
1. Used an MD5 hash generator: https://www.md5hashgenerator.com/
2. Generated MD5 hash for user ID "1"
3. Replaced the session cookie with the generated hash
4. Accessed the home page with the manipulated cookie

This cookie manipulation allowed me to impersonate user ID 1 and obtain the **sixth flag**.

---

## 🗑️ Step 7: Seventh Flag - Cookie-Based Post Deletion
**Hint:** "Deleting a post seems to take an ID that is not a number. Can you figure out what it is?"

**Analysis:**
The delete functionality used the same MD5 cookie mechanism as authentication.

**Method:**
1. Identified that post deletion used MD5-encoded user IDs
2. Found a post with ID 3 that belonged to another user: `/index.php?page=view.php&id=3`
3. Generated MD5 hash for user ID "3"
4. Used Burp Suite to intercept the delete request
5. Replaced the delete cookie with the MD5 hash of user ID 3
6. Successfully deleted another user's post

This unauthorized deletion revealed the **seventh flag**.

---

## 🏁 Captured Flags Summary
1. **Flag 1:** URL parameter manipulation to access hidden content
2. **Flag 2:** Password brute force attack (user:password)
3. **Flag 3:** Form parameter manipulation during post creation
4. **Flag 4:** Mathematical hint decoding (189 × 5 = 945)
5. **Flag 5:** Unauthorized post editing via request interception
6. **Flag 6:** Cookie manipulation to impersonate user ID 1
7. **Flag 7:** Cookie-based unauthorized post deletion

---

## ✅ Vulnerabilities Identified
1. **Insecure Direct Object References (IDOR):** URL parameters allowed access to unauthorized content
2. **Weak Authentication:** Simple passwords susceptible to brute force
3. **Broken Access Control:** Users could edit and delete others' posts
4. **Predictable Session Management:** MD5-based cookies were easily manipulated
5. **Insufficient Authorization:** No proper verification of user permissions
6. **Information Disclosure:** Mathematical hints revealed internal ID structure

---

## 🛠️ Tools Used
- **Hydra** - Password brute forcing
- **Burp Suite** - Request interception and manipulation
- **RockYou Wordlist** - Common passwords for brute force
- **MD5 Hash Generator** - Cookie manipulation
- **Browser Developer Tools** - Form inspection and analysis

---

## 📚 Key Learning Points
- Always test URL parameters for IDOR vulnerabilities
- Weak passwords are easily compromised with automated tools
- Client-side restrictions can be bypassed with proxy tools
- Predictable session tokens create serious security risks
- Mathematical hints in CTFs often correspond to internal identifiers
- Proper authorization checks are crucial for sensitive operations

---

## 🔧 Remediation Suggestions
1. Implement proper access controls with server-side validation
2. Use strong, randomly generated session tokens
3. Enforce strong password policies
4. Implement rate limiting for authentication attempts
5. Add proper authorization checks for all sensitive operations
6. Use unpredictable identifiers for internal resources