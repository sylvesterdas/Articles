---
title: "Unmasking DNS Rebinding: A Hacker's Trick to Bypass Security"
seoTitle: "Unmasking DNS Rebinding: A Hacker's Trick to Bypass Security"
seoDescription: "This article will demystify this often-overlooked security vulnerability, explaining how it works and why it matters. We'll break down the technical details"
datePublished: 2025-06-04T11:34:18.821Z
cuid: cmbhvdxhg000m07lh1y0uh6gg
slug: unmasking-dns-rebinding-a-hackers-trick-to-bypass-security
cover: https://i.ibb.co/Kx0m0cBS/17d6a7007c82.png
tags: programming, javascript, frontend, technology, python, security, backend, devops

---

## Introduction

Imagine your home network as a castle, protected by a drawbridge (your router). Normally, websites on the internet can only interact with resources outside your castle walls. But what if a clever attacker could trick your castle into letting them inside? That's essentially what a DNS rebinding attack does. This article will demystify this often-overlooked security vulnerability, explaining how it works and why it matters. We'll break down the technical details with simple examples and highlight its real-world implications.

## What is DNS Rebinding?

DNS (Domain Name System) is like the internet's phonebook. When you type `example.com` into your browser, DNS translates that name into an IP address, which is like the street address of the server hosting the website. Your browser then uses this IP address to connect to the server.

DNS rebinding is a technique where an attacker manipulates this DNS process to trick your browser into making requests to a *different* server than intended, usually a server on your *local* network. This allows them to bypass same-origin policy (SOP) restrictions, which are designed to prevent websites from accessing data from different domains.

Think of it like this: you ask for directions to "Joe's Pizza," but someone secretly changes the sign to point to your own house! Your browser, trusting the directions, ends up knocking on your own door.

## How DNS Rebinding Works: A Step-by-Step Breakdown

Here's a simplified view of how a DNS rebinding attack unfolds:

1. **The Victim Visits a Malicious Website:** The user unknowingly visits a website controlled by the attacker (e.g., `attacker.com`).
    
2. **Initial DNS Lookup:** The browser performs a DNS lookup for `attacker.com`. The attacker's server is configured to initially return the *attacker's* public IP address.
    
3. **Attacker's Server Responds:** The browser connects to the attacker's server and receives some initial HTML/JavaScript code.
    
4. **JavaScript Triggers Rebinding:** The malicious JavaScript code instructs the browser to make a new request to `attacker.com` again. This triggers *another* DNS lookup.
    
5. **The Rebound:** This time, the attacker's DNS server responds with the *local* IP address of a device on the victim's network (e.g., `192.168.1.100`, the IP address of a smart TV or router). This is the "rebinding" part.
    
6. **Browser Connects to Local Device:** The browser, believing it's still communicating with `attacker.com`, now connects to the device at `192.168.1.100`.
    
7. **Exploitation:** The attacker can now send commands to the local device, potentially exploiting vulnerabilities or accessing sensitive information. Because the browser thinks it's still talking to the original domain, it bypasses the Same-Origin Policy.
    

Here's a simple Python example showing how an attacker might configure their DNS server (using a hypothetical DNS server library):

```python
# This is a simplified example for demonstration purposes only.
# Real-world DNS server implementation is much more complex.

import time

class DNSServer:
    def __init__(self):
        self.domain_to_ip = {}

    def add_record(self, domain, ip_list, ttl):
        self.domain_to_ip[domain] = {"ips": ip_list, "ttl": ttl, "timestamp": time.time()}

    def resolve(self, domain):
        if domain in self.domain_to_ip:
            record = self.domain_to_ip[domain]
            # Check if the record is expired based on TTL
            if time.time() - record["timestamp"] < record["ttl"]:
                return record["ips"]
            else:
                # Record expired, remove it
                del self.domain_to_ip[domain]
                return None
        else:
            return None

# Example usage:
dns_server = DNSServer()

# Initial IP
dns_server.add_record("attacker.com", ["1.2.3.4"], 10) # TTL of 10 seconds

# After 10 seconds, the IP changes to a local address
# In a real attack, this change would be triggered by a new DNS request

# Simulate waiting for TTL to expire (in a real attack, this is automatic)
time.sleep(11)

# Now the DNS server updates the IP address
dns_server.add_record("attacker.com", ["192.168.1.100"], 60)

print(f"DNS Resolution for attacker.com: {dns_server.resolve('attacker.com')}")
```

**Technical Deep Dive: Time-to-Live (TTL)**

The `TTL` (Time-to-Live) value in DNS records is crucial. It specifies how long a DNS resolver (like your browser's cache) should cache the IP address. Attackers often use very short TTLs (e.g., a few seconds) to force the browser to quickly re-query the DNS server, allowing them to swap the IP address. In the example above, the initial TTL is set to 10 seconds. After that time, the IP address is updated. A TTL of 0 is sometimes used, but this can cause issues with some resolvers.

## The Role of Same-Origin Policy (SOP)

The Same-Origin Policy (SOP) is a fundamental security mechanism in web browsers. It restricts websites from making requests to a different origin (domain, protocol, and port). For example, a website hosted on `example.com` should not be able to access data from `another-website.com`.

DNS rebinding effectively *bypasses* SOP because the browser *believes* it's still talking to the original domain (`attacker.com`) even after the IP address has been changed. This is the core of the vulnerability.

## Practical Implications: Real-World Risks

DNS rebinding can have serious consequences:

* **Accessing Router Configuration:** Attackers could potentially access and modify your router's settings, changing your Wi-Fi password or opening up malicious ports.
    
* **Exploiting IoT Devices:** Many IoT devices (smart TVs, cameras, etc.) have web interfaces for configuration. An attacker could exploit vulnerabilities in these interfaces to gain control of the device.
    
* **Stealing Local Data:** If you have a local web server running (e.g., a development environment), an attacker could potentially access sensitive data stored on it.
    
* **Internal Network Scanning:** An attacker can use your browser to scan your internal network for open ports and services, gathering information for further attacks.
    

## Mitigation Strategies: Defending Against DNS Rebinding

Several strategies can help mitigate DNS rebinding attacks:

* **Router-Level Protection:** Some routers offer features to block DNS rebinding attacks, usually by preventing DNS responses that point to local IP addresses. Check your router's documentation for details.
    
* **Browser Extensions:** Browser extensions like "NoScript" can provide additional protection by blocking JavaScript code that might be used for rebinding attacks.
    
* **CORS (Cross-Origin Resource Sharing):** While DNS rebinding bypasses SOP, implementing proper CORS policies on your local web applications can still provide some protection. CORS allows you to specify which origins are allowed to access your resources. However, relying *solely* on CORS is not sufficient, as an attacker can still potentially send requests even if the browser won't process the response.
    
* **Hostname Verification:** Local web applications should verify the `Host` header of incoming requests. This header indicates the domain the browser *thinks* it's connecting to. If the `Host` header doesn't match the expected local hostname, the application should reject the request.
    
* **Address Binding:** Bind your local server to `127.0.0.1` (localhost) only. This prevents external access unless explicitly proxied.
    

Here's a simple JavaScript example of Host header verification:

```javascript
// Server-side JavaScript (e.g., Node.js)

const http = require('http');

const server = http.createServer((req, res) => {
  const allowedHost = 'localhost'; // Or your specific domain
  const hostHeader = req.headers.host;

  if (hostHeader !== allowedHost) {
    console.log(`Unauthorized request from host: ${hostHeader}`);
    res.writeHead(403, { 'Content-Type': 'text/plain' });
    res.end('Forbidden: Invalid Host header');
    return;
  }

  // Process the request if the Host header is valid
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello, world!');
});

server.listen(3000, '127.0.0.1', () => {
  console.log('Server running at [http://127.0.0.1:3000/](http://127.0.0.1:3000/)');
});
```

## Conclusion

DNS rebinding is a sneaky attack that exploits the trust browsers place in the DNS system. By understanding how it works and implementing appropriate mitigation strategies, you can significantly reduce your risk of falling victim to this type of attack. Remember to keep your routers and IoT devices updated with the latest security patches, and be cautious about visiting untrusted websites. Protecting your local network is crucial in today's interconnected world.

Inspired by an article from [https://github.blog/security/application-security/dns-rebinding-attacks-explained-the-lookup-is-coming-from-inside-the-house/](https://github.blog/security/application-security/dns-rebinding-attacks-explained-the-lookup-is-coming-from-inside-the-house/)