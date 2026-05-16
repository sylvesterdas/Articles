---
title: "Building Your Own Fortress: A Beginner's Guide to Creating a Basic Password Manager"
seoTitle: "Building Your Own Fortress: A Beginner's Guide to Creatin..."
seoDescription: "Introduction"
datePublished: 2025-05-24T00:00:31.485Z
cuid: cmb1grcal00060ajsa5ogeosq
slug: building-your-own-fortress-a-beginners-guide-to-creating-a-basic-password-manager
cover: https://i.ibb.co/DPcJmJy0/252037115a79.png
tags: programming, technology, python, security, backend, database

---

## Introduction

In today's digital world, managing passwords can feel like herding cats. We have countless accounts, each demanding a unique and strong password. Reusing passwords is a huge security risk, and remembering dozens of complex strings is practically impossible. That's where password managers come in.

This article will guide you through the fundamental concepts and steps involved in building a basic password manager using Python. While this won't be a production-ready application, it will give you a solid understanding of the underlying principles and empower you to build upon it. We'll focus on security best practices and explain the "why" behind each decision.

## Why Build Your Own?

You might be thinking, "Why bother building my own when there are already so many great password managers out there?" That's a valid question! Here's why this exercise is valuable:

*   **Understanding Security:** You'll gain a deep understanding of how password managers work and the security measures they employ. This knowledge is invaluable for protecting yourself online.
*   **Customization:** You can tailor the password manager to your specific needs and preferences.
*   **Learning Experience:** It's a fantastic way to improve your Python programming skills and learn about cryptography.
*   **Trust:** While established password managers are trustworthy, building your own gives you ultimate control and visibility.

## Core Concepts: Encryption and Key Management

At the heart of any password manager lies **encryption**. Encryption transforms your passwords into an unreadable format, protecting them from unauthorized access. We'll use a symmetric encryption algorithm, meaning the same key is used for both encryption and decryption.

**Key Management** is crucial. The encryption key must be stored securely. If an attacker gains access to the key, they can decrypt all your passwords. In our simple example, we'll explore a basic key storage method, but understand that production-level password managers employ more sophisticated techniques.

### Technical Deep Dive: Symmetric vs. Asymmetric Encryption

*   **Symmetric Encryption:** Uses the same key for both encryption and decryption. It's generally faster than asymmetric encryption. Examples include AES (Advanced Encryption Standard) and ChaCha20.
*   **Asymmetric Encryption:** Uses a pair of keys: a public key for encryption and a private key for decryption. The public key can be shared, but the private key must be kept secret. Examples include RSA and ECC (Elliptic Curve Cryptography). Asymmetric encryption is often used for key exchange and digital signatures.

For our simple password manager, symmetric encryption is suitable due to its speed and simplicity.

## Building a Simple Password Manager in Python

Let's build a basic password manager with the following features:

*   **Password Storage:**  Stores passwords in an encrypted file.
*   **Password Retrieval:**  Decrypts and retrieves passwords when needed.

Here's the Python code:

```python
import os
import hashlib
from cryptography.fernet import Fernet
from cryptography.hazmat.primitives import hashes
from cryptography.hazmat.primitives.kdf.pbkdf2 import PBKDF2HMAC
from cryptography.hazmat.backends import default_backend
import base64

# --- Key Derivation Function ---
def generate_key(password, salt=None):
    """Generates a secure encryption key from a password using PBKDF2."""
    if salt is None:
        salt = os.urandom(16)  # Generate a new random salt
    kdf = PBKDF2HMAC(
        algorithm=hashes.SHA256(),
        length=32,  # Key length in bytes (256 bits)
        salt=salt,
        iterations=390000, #Recommended by NIST
        backend=default_backend()
    )
    key = base64.urlsafe_b64encode(kdf.derive(password.encode()))
    return key, salt

# --- Encryption/Decryption Functions ---
def encrypt(data, key):
    """Encrypts data using Fernet."""
    f = Fernet(key)
    return f.encrypt(data.encode()).decode()

def decrypt(data, key):
    """Decrypts data using Fernet."""
    f = Fernet(key)
    return f.decrypt(data.encode()).decode()

# --- Password Management Functions ---
def store_password(website, username, password, master_password, filename="passwords.txt"):
    """Stores an encrypted password in a file."""
    # Derive key from master password
    key, salt = generate_key(master_password)

    # Encrypt the password
    encrypted_password = encrypt(f"{website}:{username}:{password}", key)

    # Store the salt and encrypted password
    with open(filename, "a") as f:
        f.write(f"{website}:{base64.b64encode(salt).decode()}:{encrypted_password}\n")
    print(f"Password for {website} stored successfully.")

def retrieve_password(website, master_password, filename="passwords.txt"):
    """Retrieves a decrypted password from the file."""
    try:
        with open(filename, "r") as f:
            for line in f:
                stored_website, salt_b64, encrypted_password = line.strip().split(":")
                if stored_website == website:
                    salt = base64.b64decode(salt_b64)
                    key, _ = generate_key(master_password, salt)
                    decrypted_data = decrypt(encrypted_password, key)
                    website, username, password = decrypted_data.split(":")
                    return username, password
            print(f"No password found for {website}.")
            return None, None
    except FileNotFoundError:
        print("Password file not found. Please add passwords first.")
        return None, None

# --- Example Usage ---
if __name__ == "__main__":
    master_password = input("Enter your master password: ")
    website = input("Enter the website: ")
    username = input("Enter the username: ")
    password = input("Enter the password: ")

    store_password(website, username, password, master_password)

    retrieved_username, retrieved_password = retrieve_password(website, master_password)

    if retrieved_username and retrieved_password:
        print(f"Retrieved username: {retrieved_username}")
        print(f"Retrieved password: {retrieved_password}")
```

**Explanation:**

1.  **Import necessary libraries:** `os`, `hashlib`, `cryptography`.
2.  **Key Derivation (generate_key):** This function takes your master password and generates a strong encryption key. It uses PBKDF2 (Password-Based Key Derivation Function 2), a widely used algorithm for securely deriving keys from passwords.  A `salt` is a random value added to the password before hashing, making it harder for attackers to use pre-computed tables of hashes (rainbow tables) to crack the password.  The salt is stored alongside the encrypted password.  We're also using a high iteration count (390,000) to make brute-force attacks slower.
3.  **Encryption (encrypt):** Uses the `Fernet` library, which provides symmetric encryption using AES.  It encrypts the website, username, and password combined.
4.  **Decryption (decrypt):** Uses `Fernet` to decrypt the stored password.
5.  **Password Storage (store\_password):**
    *   Takes the website, username, and password as input.
    *   Derives an encryption key from the master password using `generate_key`.
    *   Encrypts the password using `encrypt`.
    *   Stores the encrypted password, website and salt in a file (passwords.txt).
6.  **Password Retrieval (retrieve\_password):**
    *   Takes the website and master password as input.
    *   Reads the password file, searching for the entry corresponding to the specified website.
    *   Derives the encryption key from the master password and the stored salt.
    *   Decrypts the password using `decrypt`.
    *   Returns the username and password.

**How to Run:**

1.  Save the code as a `.py` file (e.g., `password_manager.py`).
2.  Run the script from your terminal: `python password_manager.py`
3.  Follow the prompts to enter your master password, website, username, and password.
4.  The password will be stored in the `passwords.txt` file.
5.  Run the script again to retrieve the password.

## Practical Implications and Security Considerations

This is a very basic implementation and has several limitations:

*   **Key Storage:** The master password is used to derive the encryption key each time.  This means the security relies entirely on the strength of your master password. If your master password is weak, an attacker can easily crack it and decrypt your passwords.
*   **File Storage:** Storing the encrypted passwords in a plain text file is not ideal. A more secure approach would be to use a database.
*   **No Protection Against Keyloggers:** This implementation doesn't protect against keyloggers, which can capture your master password as you type it.
*   **Lack of Features:** It lacks features like password generation, password strength checking, and multi-factor authentication.
*   **Salt Storage:** The salt is stored in plain text along with the encrypted password. While this is necessary for decryption, it's important to understand that an attacker who gains access to the password file also gains access to the salts.

**Recommendations for Improvement:**

*   **Use a Database:** Store passwords in an encrypted database (e.g., SQLite with encryption).
*   **Implement Key Stretching:** Use a strong key derivation function like Argon2 to make brute-force attacks more difficult.
*   **Consider Hardware Security Modules (HSMs):** For very sensitive applications, consider using HSMs to store the encryption key securely.
*   **Add Password Generation:** Generate strong, random passwords for each account.
*   **Implement Multi-Factor Authentication:** Add an extra layer of security by requiring a second factor, such as a code from your phone.

## Conclusion

Building your own password manager, even a simple one, is a valuable learning experience. It helps you understand the underlying principles of password security and appreciate the complexities involved in building a secure application. This example provides a starting point for further exploration and experimentation. Remember to prioritize security and continuously learn about best practices to protect your data in the ever-evolving digital landscape.

Inspired by an article from [https://hackernoon.com/heres-the-code-you-need-to-build-a-secure-password-manager-in-python?source=rss](https://hackernoon.com/heres-the-code-you-need-to-build-a-secure-password-manager-in-python?source=rss)