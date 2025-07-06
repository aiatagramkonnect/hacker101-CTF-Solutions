# Grayhatcon CTF

**Difficulty:** Moderate  
**Category:** Web  
**Flags:** 0/4  
**Status:** 🔄 Work in Progress

---

## 🧠 Overview

This web challenge involves bypassing IP-based access controls to access a restricted admin directory. The challenge requires understanding various IP spoofing techniques and HTTP header manipulation to circumvent the `.htaccess` restrictions.

---

## 🔍 Step 1: Initial Reconnaissance

### Directory Discovery
First, I performed directory enumeration using dirsearch and discovered that `robots.txt` exists on the target server.

### Robots.txt Analysis
Accessing `robots.txt` revealed:

![Robots.txt Content](image.png)

This disclosed the existence of a secret admin directory: `s3cr3t-4dm1n/`

---

## 🚫 Step 2: Access Restriction Discovery

### Initial Access Attempt
When attempting to access the `/s3cr3t-4dm1n/` directory, I encountered a **403 Forbidden** error:

![403 Forbidden Error](Robots.png)

This indicated that access controls were in place preventing direct access to the admin directory.

---

## 🔧 Step 3: Further Reconnaissance

### .htaccess Discovery
Using dirsearch, I discovered that the `.htaccess` file was accessible:

![Dirsearch Results](Dirsearch.png)

### .htaccess Analysis
The server allowed me to download the `.htaccess` file, which contained the following access control rules:

```apache
Order Deny,Allow
Deny from all
Allow from 8.8.8.8
Allow from 8.8.4.4
```

**Analysis:** The configuration denies access from all IPs except:
- `8.8.8.8` (Google DNS)
- `8.8.4.4` (Google DNS)

---

## 🎯 Step 4: IP Spoofing Attempts

### X-Forwarded-For Header
I attempted to bypass the IP restriction using the `X-Forwarded-For` header:

```bash
curl https://dc7a8086fb2e1037e34fbb0e7d606d63.ctf.hacker101.com/s3cr3t-4dm1n/ \
  -H "X-Forwarded-For: 8.8.4.4"
```

**Result:** Still received 403 Forbidden error.

### Current Status
The basic IP spoofing attempt failed. This suggests that either:
1. The server is not configured to trust the `X-Forwarded-For` header
2. Additional headers are required
3. A different IP spoofing technique is needed

---

## 🔄 Next Steps and Alternative Approaches

### Additional Headers to Try:
1. **X-Real-IP**: Another common header for IP forwarding
   ```bash
   curl -H "X-Real-IP: 8.8.8.8" [URL]
   ```

2. **X-Originating-IP**: Sometimes used by mail servers and proxies
   ```bash
   curl -H "X-Originating-IP: 8.8.8.8" [URL]
   ```

3. **X-Remote-IP**: Alternative IP header
   ```bash
   curl -H "X-Remote-IP: 8.8.8.8" [URL]
   ```

4. **X-Client-IP**: Client IP header
   ```bash
   curl -H "X-Client-IP: 8.8.8.8" [URL]
   ```

### Combined Header Approach:
Try multiple headers simultaneously:
```bash
curl https://dc7a8086fb2e1037e34fbb0e7d606d63.ctf.hacker101.com/s3cr3t-4dm1n/ \
  -H "X-Forwarded-For: 8.8.8.8" \
  -H "X-Real-IP: 8.8.8.8" \
  -H "X-Originating-IP: 8.8.8.8" \
  -H "X-Remote-IP: 8.8.8.8" \
  -H "X-Client-IP: 8.8.8.8"
```

### Alternative IP Formats:
1. **IPv6 format**: Try converting the IPs to IPv6
2. **Decimal format**: Convert IP to decimal representation
3. **Hex format**: Convert IP to hexadecimal

### Proxy Chain Simulation:
```bash
curl -H "X-Forwarded-For: 192.168.1.1, 8.8.8.8" [URL]
```

---

## 🛠️ Tools and Techniques

- **dirsearch** - Directory enumeration
- **curl** - HTTP request manipulation
- **Burp Suite** - Web application testing (mentioned for 403 bypass attempts)

---

## 📚 Technical Notes

### .htaccess IP Restriction Bypass Methods:
1. **Header Manipulation** - Using various IP forwarding headers
2. **Proxy Spoofing** - Simulating requests through allowed proxies
3. **IPv6 Conversion** - Converting allowed IPs to different formats
4. **Double Encoding** - URL encoding the IP addresses

### Common IP Spoofing Headers:
- `X-Forwarded-For`
- `X-Real-IP`
- `X-Originating-IP`
- `X-Remote-IP`
- `X-Client-IP`
- `X-Forwarded-Host`
- `X-Remote-Addr`

---

## 🎯 Current Challenge Status

- ✅ **Reconnaissance Complete** - Found admin directory and access controls
- ✅ **.htaccess Analysis** - Understand IP restrictions (8.8.8.8, 8.8.4.4)
- ❌ **Initial Bypass Attempt** - X-Forwarded-For header unsuccessful
- 🔄 **Next Phase** - Try alternative IP spoofing techniques

---

## 💡 Lessons Learned So Far

1. **Always check robots.txt** - Can reveal hidden directories
2. **Enumerate accessible configuration files** - .htaccess, .env, etc.
3. **Understand access control mechanisms** - IP restrictions, user agents, etc.
4. **Multiple bypass techniques exist** - Don't stop at first failed attempt

---

**Challenge Status: 🔄 In Progress (0/4 flags captured)**

*Need to continue experimenting with different IP spoofing techniques and headers to bypass the .htaccess restrictions.*