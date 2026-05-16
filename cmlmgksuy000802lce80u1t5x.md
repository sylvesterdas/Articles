---
title: "Hardening Your Docker Images: From Zero to Secure with Open Source Tools"
seoTitle: "Secure Docker Images with Open Source Tools"
seoDescription: "Secure Docker images with open-source tools: use CycloneDX SBOMs and VEX documents for improved vulnerability management"
datePublished: 2026-02-14T15:15:39.418Z
cuid: cmlmgksuy000802lce80u1t5x
slug: hardening-your-docker-images-from-zero-to-secure-with-open-source-tools
cover: https://i.ibb.co/MxX6t4mR/5a7e1e5f46c7.png
tags: docker, programming, javascript, technology, python, security, devops, containerization, sbom, content-hardening, cyclone-dx

---

In today's world, containers are the backbone of many software deployments. Docker, the leading containerization platform, allows us to package applications and their dependencies into portable, isolated units. However, simply creating a Docker image isn't enough. Security is paramount. This article will guide you through hardening your Docker images using open-source tools and best practices, focusing on creating fully compliant, secure containers. We'll cover building from scratch, generating Software Bill of Materials (SBOMs) with CycloneDX 1.6, and creating Vulnerability Exploitability eXchange (VEX) documents.

## Core Concept: Container Hardening

Container hardening is the process of securing a container image and its runtime environment to minimize the attack surface and reduce the risk of vulnerabilities being exploited. It involves several key strategies, including:

* **Minimizing the base image:** Reducing the number of installed packages and dependencies.
    
* **Using non-root users:** Preventing processes from running with elevated privileges.
    
* **Implementing security policies:** Defining rules and restrictions for container behavior.
    
* **Regularly scanning for vulnerabilities:** Identifying and addressing potential weaknesses.
    
* **Generating SBOMs:** Creating an inventory of all software components within the image.
    
* **Creating VEX documents:** Providing information about whether known vulnerabilities are exploitable in the specific context of your application.
    

## Building from Scratch: The "Scratch" Base Image

One of the most effective ways to harden a Docker image is to start with a "scratch" base image. The `scratch` image is essentially an empty container. This means you only include the absolute minimum required for your application to run.

**Why is this important?**

Traditional base images like `ubuntu` or `alpine` come with a lot of pre-installed packages and utilities. While convenient, these packages can introduce unnecessary vulnerabilities and increase the attack surface. By starting with `scratch`, you eliminate this risk.

**Example:**

Let's say you have a simple "Hello, World!" application written in Go. Here's how you would build a Docker image using `scratch`:

```dockerfile
# Stage 1: Build the Go application
FROM golang:1.21 AS builder
WORKDIR /app
COPY . .
RUN go build -o main .

# Stage 2: Create the final image using scratch
FROM scratch
WORKDIR /app
COPY --from=builder /app/main /app/main
EXPOSE 8080
ENTRYPOINT ["/app/main"]
```

**Explanation:**

1. `FROM golang:1.21 AS builder`: This line uses the official Go image to build our application. We name this stage "builder".
    
2. `WORKDIR /app`: Sets the working directory inside the container.
    
3. `COPY . .`: Copies the source code to the container.
    
4. `RUN go build -o main .`: Compiles the Go application into an executable named "main".
    
5. `FROM scratch`: This is where the magic happens. We start with an empty container.
    
6. `COPY --from=builder /app/main /app/main`: We copy the compiled executable from the "builder" stage to the `scratch` container.
    
7. `EXPOSE 8080`: Exposes port 8080.
    
8. `ENTRYPOINT ["/app/main"]`: Specifies the command to run when the container starts.
    

**Important Note:** This approach requires a compiled binary. It's suitable for languages like Go, Rust, or C/C++. For interpreted languages like Python or JavaScript, you'll need to include a minimal runtime environment.

## Generating SBOMs with CycloneDX 1.6

A Software Bill of Materials (SBOM) is a comprehensive inventory of all software components used in your application. It's like a recipe for your software, listing all the ingredients. CycloneDX is a widely adopted open-source standard for SBOMs. Version 1.6 introduces significant improvements in expressiveness and support for various software ecosystems.

**Why are SBOMs important?**

* **Vulnerability Management:** Knowing what's in your software allows you to quickly identify and address vulnerabilities when they are discovered.
    
* **License Compliance:** SBOMs help you track the licenses of your dependencies, ensuring compliance with open-source licenses.
    
* **Supply Chain Security:** SBOMs provide transparency and visibility into the software supply chain.
    

**Generating an SBOM with CycloneDX:**

Several tools can generate CycloneDX SBOMs. Let's look at an example using the `syft` tool from Anchore.

**Prerequisites:**

* Install `syft`: `curl -sSfL [https://raw.githubusercontent.com/anchore/syft/main/install.sh](https://raw.githubusercontent.com/anchore/syft/main/install.sh) | sh -s -- -b /usr/local/bin`
    

**Example:**

To generate an SBOM for a Docker image:

```bash
syft your-image-name -o cyclonedx-json > sbom.cdx.json
```

This command generates an SBOM in CycloneDX JSON format and saves it to `sbom.cdx.json`.

**Technical Deep Dive:** CycloneDX 1.6 introduces enhanced support for component metadata, including supplier information, external references, and more granular dependency relationships. It also improves the ability to represent different types of software components, such as libraries, frameworks, and applications.

## Creating VEX Documents

A Vulnerability Exploitability eXchange (VEX) document provides information about the exploitability of known vulnerabilities in the context of a specific product or application. Just because a vulnerability exists in a component doesn't necessarily mean it's exploitable in your particular setup. A VEX document clarifies this.

**Why are VEX documents important?**

* **Prioritized Remediation:** VEX helps you focus on vulnerabilities that are actually exploitable in your environment.
    
* **Reduced Noise:** It filters out irrelevant vulnerability reports, saving you time and effort.
    
* **Improved Communication:** VEX provides a standardized way to communicate vulnerability exploitability information.
    

**Creating a VEX Document:**

Creating a VEX document is a manual process that requires careful analysis of your application and its dependencies. You need to determine whether a vulnerability is exploitable based on factors such as:

* **Configuration:** Is the vulnerable component configured in a way that makes it exploitable?
    
* **Deployment Environment:** Are there mitigating factors in the deployment environment?
    
* **Code Usage:** Is the vulnerable code path actually used by your application?
    

**Example:**

Let's say your SBOM reveals that you are using a library with a known vulnerability. After analyzing your application, you determine that the vulnerable code path is never executed. In this case, you would create a VEX document indicating that the vulnerability is "not\_affected".

VEX documents are typically created in CSAF (Common Security Advisory Framework) format, which is an industry standard.

**Tools and Resources:**

While there aren't automated tools to *create* VEX documents (because it requires contextual analysis), there are tools to *consume* and *validate* them. The CycloneDX ecosystem includes tools for working with CSAF/VEX documents.

**Example (Conceptual):**

```json
{
  "document": {
    "title": "VEX Document for My Application",
    "publisher": {
      "name": "My Company",
      "type": "vendor"
    }
  },
  "vulnerabilities": [
    {
      "cve": "CVE-2023-12345",
      "product_status": {
        "first_affected": [
          "component": "MyComponent-1.0"
        ],
        "known_not_affected": [
          "component": "MyComponent-1.0"
        ]
      },
      "justification": "Vulnerable code path is not executed in this application."
    }
  ]
}
```

**Explanation:**

This is a simplified example. It indicates that CVE-2023-12345 affects `MyComponent-1.0` in general, but it is `known_not_affected` in the specific context of this application because the vulnerable code path is not used.

## Practical Implications

By implementing these techniques, you can significantly improve the security posture of your Docker images. This reduces the risk of successful attacks and helps you comply with security regulations and industry best practices.

## Conclusion

Hardening Docker images is a crucial aspect of modern software development. By using techniques like building from scratch, generating SBOMs with CycloneDX, and creating VEX documents, you can create more secure and compliant containers. While it requires effort and attention to detail, the benefits of a hardened container environment are well worth the investment. Remember to stay updated on the latest security best practices and tools to continuously improve your container security posture.