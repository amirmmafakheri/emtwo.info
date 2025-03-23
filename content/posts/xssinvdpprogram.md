+++
date = '2025-03-15T13:41:28Z'
draft = false
title = 'Reflected Cross-Site Scripting (XSS) via searchKey Parameter'
+++

![Alt Text](/images/1.jpg)

## Summary

A reflected Cross-Site Scripting (XSS) vulnerability exists on the Century 21 Commercial website due to insufficient input sanitization in the `searchKey` parameter. This allows an attacker to inject and execute arbitrary JavaScript in a victim’s browser, potentially leading to session hijacking, credential theft, or other malicious actions.

---

## Affected URL
[https://commercial.century21.com/real-estate/roscommon-county-mi/LNMIROSCOMMON](https://commercial.century21.com/real-estate/roscommon-county-mi/LNMIROSCOMMON)
## Vulnerable Parameter
`searchKey`
## Proof of Concept (PoC)
### Steps to Reproduce
1. Open the following URL in a browser:
   
   ```
   https://commercial.century21.com/real-estate/roscommon-county-mi/LNMIROSCOMMON/?searchKey=%22%3E%3Cscript%3Ealert(origin)%3C/script%3E
   ```
   
2. The browser will execute the injected JavaScript, displaying an alert box with the origin of the website.

### Payload Used
```html
"><script>alert(origin)</script>
```

## Technical Analysis
The vulnerability exists because the application does not properly sanitize user input before rendering it in the HTML response. When a user-controlled value is injected directly into the page without escaping or encoding, an attacker can insert arbitrary JavaScript code that executes within the victim’s browser.

### Attack Scenario
1. **Phishing Attack**: An attacker can craft a malicious URL containing an XSS payload and trick users into clicking it via social engineering or email phishing campaigns.
2. **Session Hijacking**: If the application does not implement proper cookie protections (`HttpOnly`, `Secure`, and `SameSite` flags), an attacker can steal authentication cookies and gain unauthorized access.
3. **Page Defacement**: The attacker can manipulate webpage content, displaying misleading or fraudulent information to users.
4. **Keylogging & Data Exfiltration**: Malicious scripts can be used to log keystrokes or steal sensitive form data entered by the user.

## Impact
- **Confidentiality Breach:** Attackers can extract sensitive data from the DOM or steal session cookies.
- **Integrity Violation:** The attacker can alter page content and inject malicious scripts.
- **Availability Risk:** Users may be redirected to phishing or malware-infected sites, disrupting their experience.

## Recommendations
To remediate this issue, the following security measures should be implemented:

### 1. Input Sanitization
Escape special characters (`<`, `>`, `"`, `'`, `&`) before rendering user input in the response.

### 2. Content Security Policy (CSP)
Implement a strong CSP to restrict script execution to trusted sources:
```html
Content-Security-Policy: default-src 'self'; script-src 'self' https://trusted.cdn.com;
```

### 3. HTTPOnly and Secure Cookies
Ensure authentication cookies are set with the `HttpOnly`, `Secure`, and `SameSite` flags to prevent client-side access.

### 4. User Input Validation
Validate and sanitize all user-controlled input to allow only expected values. Implement server-side validation to reject potentially harmful input.

### 5. Use Output Encoding
Encode output to prevent script execution:
```html
&lt;script&gt;alert("XSS")&lt;/script&gt;
```

## Severity
**High** (Reflected XSS can be exploited for phishing, credential theft, and session hijacking)

## References
- [OWASP XSS Prevention Cheat Sheet](https://cheatsheetseries.owasp.org/cheatsheets/XSS_Prevention_Cheat_Sheet.html)
- [MDN Web Docs - Content Security Policy](https://developer.mozilla.org/en-US/docs/Web/HTTP/CSP)
- [Google XSS Prevention Guide](https://developer.chrome.com/docs/security/xss/)

---

This report aims to highlight the security risk posed by reflected XSS vulnerabilities and provide actionable remediation steps. If you have any questions or need further clarification, feel free to reach out!

