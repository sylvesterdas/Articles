---
title: "Implementing Antivirus Scanning in Node.js Applications with ClamAV"
seoTitle: "Secure Node.js File Uploads: Integrate ClamAV for Virus Scanning"
seoDescription: "Learn how to secure Node.js file uploads by integrating ClamAV, a free and open-source antivirus engine. Protect your applications from malicious files with this practical guide."
datePublished: 2026-04-21T19:29:39.860Z
cuid: cmo90pogi00e81qoce8l48m0h
slug: implementing-antivirus-scanning-in-node-js-applications-with-clamav
cover: https://cdn.hashnode.com/uploads/covers/671caf41dfd58b3cce970f53/658e54b0-28c2-4164-b9db-206a45238886.png
ogImage: https://cdn.hashnode.com/uploads/og-images/671caf41dfd58b3cce970f53/307f11b6-7467-4662-9e73-52a5033ef52d.png
tags: javascript, security, node-js, antivirus, clamav, file-uploads

---

Accepting file uploads in your Node.js application is a common feature, but it also introduces a significant security vulnerability. Malicious files can compromise your server, spread infections, or facilitate data breaches. Implementing robust antivirus scanning for all incoming files is a critical defense line.

### ClamAV: Your Free, Open-Source Antivirus Solution

![](https://cdn.hashnode.com/uploads/covers/671caf41dfd58b3cce970f53/ef9e4921-7971-471e-8ec9-6ebea16fb663.png align="center")

ClamAV is a widely used, free, and open-source antivirus engine ideal for scanning web uploads and email attachments. Maintained by Cisco, it offers a self-hosted solution, meaning your files stay on your server, addressing privacy concerns. Its key benefits include no subscription fees, no external API dependencies, and a mature, battle-tested codebase with a comprehensive signature database.

### Setting Up ClamAV on Your Server

Before integrating, install ClamAV and its daemon (`clamd`) on your server. The daemon runs in the background, providing faster scanning than command-line invocations.

**1\. Installation (Linux example):**

```bash
sudo apt update
sudo apt install clamav clamav-daemon
```

For macOS, use Homebrew: `brew install clamav`.

**2\. Update Virus Definitions:**

Keep ClamAV effective by ensuring its virus definitions are up-to-date. `freshclam` handles this. Verify it runs periodically (often automatically or via cron).

```plaintext

bash
sudo freshclam
```

**3\. Start the ClamAV Daemon:**

Ensure `clamd` is running and enabled to start on boot:

```plaintext

bash
sudo systemctl status clamav-daemon
sudo systemctl start clamav-daemon
sudo systemctl enable clamav-daemon
```

Verify with `clamdscan --version`.

### Integrating ClamAV with Node.js using `pompelmi`

![](https://cdn.hashnode.com/uploads/covers/671caf41dfd58b3cce970f53/63ad3d36-d9d2-427e-b808-181df86e60d9.png align="center")

The `pompelmi` Node.js package simplifies interaction with the `clamd` daemon, offering a stream-based interface.

**1\. Install** `pompelmi`**:**

```plaintext

bash
npm install pompelmi
```

**2\. Scan a File Stream:**

Use `pompelmi`'s `scanStream` function with any readable stream, such as from an uploaded file.

```plaintext

javascript
const { scanStream } = require('pompelmi');
const fs = require('fs');
const path = require('path');

async function scanUploadedFile(filePath) {
    try {
        const fileStream = fs.createReadStream(filePath);
        const result = await scanStream(fileStream); // Send stream to ClamAV

        if (result.isInfected) {
            console.log(`File "${path.basename(filePath)}" is infected! Found: ${result.viruses.join(', ')}`);
            // CRITICAL: Delete the file, reject the upload. Do NOT process infected files.
            return false;
        } else {
            console.log(`File "${path.basename(filePath)}" is clean.`);
            // Proceed with storing or processing the file.
            return true;
        }
    } catch (error) {
        console.error(`Antivirus scan failed for "${path.basename(filePath)}":`, error.message);
        // Implement robust error handling (e.g., ClamAV daemon unreachable).
        // A scan failure should typically lead to file rejection for security.
        return false;
    }
}

// --- Testing your setup with the EICAR test file ---
// The EICAR test file is a safe, non-viral string detected by all AV software.
// DO NOT use real viruses for testing.
const eicarFilePath = path.join(__dirname, 'eicar.txt');
fs.writeFileSync(eicarFilePath, 'X5O!P%@AP[4\\PZX54(P^)7CC)7}$EICAR-STANDARD-ANTIVIRUS-TEST-FILE!$H+H*');

console.log('Scanning an EICAR test file...');
scanUploadedFile(eicarFilePath)
    .then(isClean => console.log(`Scan result for EICAR file: ${isClean ? 'Clean' : 'Infected/Error'}`))
    .finally(() => fs.unlinkSync(eicarFilePath)); // Clean up test file
```

The `result` object from `scanStream` indicates if `isInfected` and lists `viruses` found. Immediate action is required for infected files.

### Important Considerations

*   **Performance**: Scanning consumes resources. For high-volume uploads, consider dedicated ClamAV scanning servers to offload your main application.
    
*   **Definition Updates**: Regularly update virus definitions via `freshclam` to maintain protection against new threats.
    
*   **False Positives/Negatives**: No AV is perfect. Combine scanning with other security measures like file type validation and input sanitization.
    
*   **Error Handling**: A robust implementation must handle cases where the ClamAV daemon is unavailable. For security, treat scan failures as rejections.
    
*   **Resource Usage**: Monitor server memory and CPU, especially during heavy scanning.
    

### Conclusion

Integrating ClamAV into your Node.js applications provides a vital layer of defense against malicious file uploads. By leveraging this free, open-source solution, you can enhance your application's security posture, protect your infrastructure, and safeguard your users' data. Always combine AV scanning with a comprehensive security strategy.