# E-commerce CTF Challenge
**Difficulty:** [To be updated]  
**Category:** Web  
**Flags:** 1/3 (Partial completion)

---

## 🧠 Thought Process
This challenge appeared to be an e-commerce application with shopping cart functionality, administrative panels, and user-generated content features. Based on the hints provided, I identified three main attack vectors:

1. **Price manipulation** in the checkout process
2. **Administrative panel access** through credential attacks
3. **Cross-Site Scripting (XSS)** in user input fields

The challenge required a combination of request manipulation, brute force attacks, and XSS exploitation to capture all flags.

---

## 🔍 Step 1: Initial Reconnaissance
I started by exploring the application's functionality, focusing on the shopping cart and checkout process based on the first hint mentioning that "Something looks out of place with checkout" and "It's always nice to get free stuff."

Initial observations:
- E-commerce platform with product catalog (kittens and puppies)
- Shopping cart functionality
- Checkout process with price calculations
- Administrative login panel
- User input fields for comments/reviews

---

## 🚩 Step 2: First Flag - Price Manipulation via Request Tampering
**Hint Analysis:**
- *"Something looks out of place with checkout"*
- *"It's always nice to get free stuff"*

This strongly suggested a price manipulation vulnerability in the checkout process.

**Exploitation Process:**
1. Added items to cart (kitten and puppy)
2. Proceeded to checkout
3. Intercepted the checkout request using Burp Suite
4. Analyzed the request parameters for price-related fields

**Discovery:**
When examining the checkout request with Burp Suite, I found suspicious parameters that controlled the pricing. The highlighted content in the intercepted request revealed price manipulation was possible.

![Modified Request](ModifiedRequest.png)

**Successful Exploitation:**
By modifying the price parameters in the intercepted request, I was able to manipulate the total cost, essentially getting the items for free. After sending the modified request, the first flag was revealed.

![Cart Checkout](CartCheckout.png)

### 🔬 Technical Explanation: Client-Side Price Manipulation

This vulnerability occurs when e-commerce applications trust client-side data for critical calculations like pricing. Common implementation flaws include:

1. **Client-Side Price Storage:** Prices stored in hidden form fields or JavaScript variables
2. **Inadequate Server-Side Validation:** No verification of price integrity on the server
3. **Trust in HTTP Parameters:** Direct use of client-submitted price data
4. **Missing Price Lookups:** Not re-calculating prices based on product IDs server-side

**Attack Vector:**
- Intercept checkout requests using proxy tools
- Modify price parameters to reduce or zero out costs
- Submit tampered requests to complete fraudulent transactions

---

## 🔐 Step 3: Second Flag - Administrative Panel Access (In Progress)
**Hint Analysis:**
- *"There must be a way to administer the app"*

This indicated the existence of an administrative interface that required credential-based access.

**Discovery Process:**
1. **Directory Enumeration:** Attempted to locate admin panels using dirsearch
2. **Manual Path Discovery:** Quickly identified `/login` as the administrative login page

![Login Page](Login.png)

**Attack Strategy:**
I attempted multiple approaches to gain administrative access:

1. **Credential Brute Force:** 
   - Initially used Burp Suite for brute force attacks
   - Found Burp Suite too slow for this task
   - Switched to Hydra for more efficient brute forcing
   - Targeted both username and password fields

2. **SQL Injection Testing:**
   - Attempted common SQL injection payloads
   - Found that basic SQL injection techniques were blocked or protected
   - Application appeared to have some SQL injection mitigation

**Current Status:**
- Administrative login page identified
- Brute force attack methodology established
- SQL injection protection confirmed
- **Flag not yet captured** due to time constraints of brute force process

**Note:** In real-world scenarios, aggressive brute force attacks would trigger rate limiting, IP bans, or account lockouts. This approach only works in CTF environments without such protections.

---

## ✏️ Step 4: Third Flag - Cross-Site Scripting (XSS) Exploitation (Theory)
**Discovery:**
During exploration, I found an interesting endpoint: `edit?id=0`

This endpoint allowed editing of pictures/content even without administrative privileges, suggesting an authorization bypass vulnerability.

**Planned XSS Attack Vector:**
1. **Injection Point:** User comments or picture upload/edit functionality
2. **Payload Delivery:** Inject XSS payload through comment system or image metadata
3. **Trigger Mechanism:** Save malicious content and revisit the page
4. **Flag Extraction:** XSS payload executes and reveals the flag

**Potential XSS Locations:**
- Comment sections on product pages
- Image upload/edit functionality
- User profile fields
- Product review systems

**Current Status:**
- XSS injection points identified
- Attack methodology planned
- **Flag not yet captured** due to incomplete implementation

---

## 🔧 Tools and Techniques Used
- **Burp Suite** - For request interception and modification
- **Hydra** - For credential brute force attacks
- **Dirsearch** - For directory enumeration (attempted)
- **Manual Testing** - For path discovery and vulnerability identification

---

## 🏁 Captured Flags
- **Flag 1:** ✅ Price manipulation through request tampering
- **Flag 2:** ❌ Administrative access (brute force in progress)
- **Flag 3:** ❌ XSS exploitation (methodology identified)

---

## ✅ Summary
This challenge demonstrated several common web application vulnerabilities:

### Successfully Exploited:
1. **Price Manipulation:** Client-side price control allowing fraudulent transactions

### Identified but Not Completed:
2. **Weak Authentication:** Administrative panel susceptible to brute force attacks
3. **Authorization Bypass:** Ability to edit content without proper permissions
4. **Cross-Site Scripting:** User input fields vulnerable to XSS injection

### Key Vulnerabilities:
- **Insufficient Input Validation:** Price parameters trusted from client-side
- **Weak Password Policies:** Administrative accounts vulnerable to dictionary attacks
- **Missing Access Controls:** Edit functionality accessible without proper authorization
- **Inadequate Output Encoding:** User content not properly sanitized

---

## 🔧 Prevention Recommendations
1. **Server-Side Price Validation:** Always recalculate prices server-side based on product IDs
2. **Strong Authentication:** Implement account lockouts, rate limiting, and strong password policies
3. **Proper Authorization:** Verify user permissions for all sensitive operations
4. **Input Sanitization:** Implement comprehensive XSS protection through proper encoding
5. **Security Headers:** Deploy Content Security Policy (CSP) and other security headers

---

## 🎯 Key Learning Points
- Client-side price manipulation is a critical vulnerability in e-commerce applications
- Administrative panels are high-value targets requiring strong authentication
- Authorization bypass vulnerabilities can lead to privilege escalation
- XSS vulnerabilities in user-generated content can compromise application security
- CTF environments allow aggressive testing techniques not suitable for real-world applications

---

## 📝 Challenge Notes
- This challenge requires significant time investment for brute force attacks
- The difficulty level is moderate despite the time requirements
- Multiple attack vectors provide good practice for comprehensive web application testing
- Real-world applications would have additional protections making these attacks more difficult