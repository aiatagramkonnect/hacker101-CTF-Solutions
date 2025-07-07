# RTFM - CTF Challenge Writeup
**Difficulty:** Moderate  
**Category:** Web  
**Flags:** 3/8

---

## 🧠 Thought Process
This challenge appeared to be a web API pentesting scenario. The hint mentioned using dirsearch to find endpoints, which suggested this was about API enumeration and discovery. The challenge name "RTFM" (Read The F***ing Manual) hinted that documentation or API specs would be key.

---

## 🔍 Step 1: Directory Enumeration
Following the first hint, I used dirsearch to enumerate endpoints. After extensive searching, I discovered a crucial endpoint:

```
/v2/swagger.json
```

This Swagger/OpenAPI documentation file revealed the API structure and available endpoints.

![First Flag Discovery](FirstFlag.png)

**Flag 1 Captured:** Found in the Swagger documentation

---

## 🚪 Step 2: API User Endpoint Discovery
Through enumeration, I found the user management endpoint:
```
/api/v1/user
```

When sending a POST request without parameters:
```bash
curl -X POST https://7e157d6d1c713e22bd907d8987468529.ctf.hacker101.com/api/v1/user
```

**Response:**
```json
{
  "error": "Missing username and password field"
}
```

---

## 🔐 Step 3: User Registration Testing
Testing user registration with random credentials:
```bash
curl -X POST https://7e157d6d1c713e22bd907d8987468529.ctf.hacker101.com/api/v1/user \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=asdasdasdadmin&password=secresadasdasdt"
```

**Response:**
```json
{
  "username": "asdasdasdadmin",
  "flag": "^FLAG^619579aa2aaf4537a506db8f1c53b035bdab6963535d50cd0edf8c610f7b7539$FLAG$",
  "message": "User created go to /api/v1/user/login to login"
}
```

**Flag 2 Captured:** Revealed during user registration

However, when trying to register with username "admin":
```bash
curl -X POST https://7e157d6d1c713e22bd907d8987468529.ctf.hacker101.com/api/v1/user \
  -H "Content-Type: application/x-www-form-urlencoded" \
  -d "username=admin&password=secresadasdasdt"
```

**Response:**
```json
{
  "error": "Username already exists"
}
```

This confirmed that an admin user already existed in the system.

---

## ⚙️ Step 4: Configuration Endpoint
I discovered the configuration endpoint:
```
/api/v1/config
```

Accessing this URL directly revealed the third flag:

![Third Flag Discovery](ThirdFlag-1.png)

**Flag 3 Captured:** Found in the configuration endpoint

---

## 🛠️ Step 5: Additional Enumeration
Using gobuster for further directory discovery:
```bash
gobuster dir -w directory-list-lowercase-2.3-big.txt -u https://7e157d6d1c713e22bd907d8987468529.ctf.hacker101.com/api/v1/
```

This revealed another endpoint:
```
/api/v1/status
```

---

## 🏁 Flags Captured (3/8)
1. **Flag 1:** Found in `/v2/swagger.json` 
2. **Flag 2:** `^FLAG^619579aa2aaf4537a506db8f1c53b035bdab6963535d50cd0edf8c610f7b7539$FLAG$`
3. **Flag 3:** Found in `/api/v1/config`

---

## 📚 Key Takeaways
- **API Documentation:** Always check for Swagger/OpenAPI documentation (`/swagger.json`, `/v2/swagger.json`)
- **User Registration:** Test user registration endpoints for information disclosure
- **Configuration Endpoints:** Look for `/config` or similar endpoints that might expose sensitive information
- **Directory Enumeration:** Use tools like dirsearch and gobuster to discover hidden endpoints
- **Admin User Enumeration:** Test common usernames like "admin" to identify existing accounts

---

## 🔄 Next Steps
There are still 5 more flags to discover. Potential areas to explore:
- The `/api/v1/user/login` endpoint mentioned in the registration response
- The `/api/v1/status` endpoint found through gobuster
- Authentication bypass techniques
- Parameter manipulation and injection attacks
- JWT token analysis if authentication is successful