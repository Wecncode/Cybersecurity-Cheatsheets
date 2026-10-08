## 04_cryptography_and_hashes/encoding_vs_encryption.md

A common pitfall for cybersecurity learners is confusing encoding, encryption, and hashing. Understanding the intent behind how data is transformed allows you to choose the correct tool to read it.

### The Golden Rule

* **Encoding** maintains data usability and transportability. It hides nothing.
* **Encryption** maintains data confidentiality. It hides data behind a key.
* **Hashing** maintains data integrity. It verifies data without revealing it.

---

# Encoding (Format Transformation)

Encoding transforms data into a new format using a publicly available scheme. There is no key or password. Anyone who recognizes the format can instantly reverse it. In CTFs and web testing, attackers encode payloads to bypass Web Application Firewalls (WAFs) or input filters.

| Type | Identifier / Looks Like | Purpose & Decoding Tool |
| --- | --- | --- |
| **Base64** | Alphanumeric string, often ends in `=` or `==` padding. (e.g., `YWRtaW4=`) | Used to safely transfer binary data over text-based protocols (like HTTP/Email).<br>

<br>**CLI:** `echo "YWRtaW4=" | base64 -d` |
| **URL Encoding** | Uses `%` followed by two hex digits. (e.g., `%3Cscript%3E` for `<script>`) | Ensures special characters safely pass through web URLs.<br>

<br>**Tool:** Burp Suite Decoder or CyberChef. |
| **Hexadecimal** | Numbers `0-9` and letters `A-F` or `a-f`. Often prefixed with `0x` or `\x`. | Human-readable representation of binary data.<br>

<br>**CLI:** `echo "61646d696e" | xxd -r -p` |
| **HTML Entities** | Starts with `&` and ends with `;`. (e.g., `&lt;` for `<`) | Prevents browsers from interpreting text as HTML code.<br>

<br>**Tool:** CyberChef. |
| **Binary** | `0`s and `1`s. (e.g., `01100001`) | The lowest-level data representation.<br>

<br>**Tool:** CyberChef (From Binary). |

> **Pro Tip:** **CyberChef** (gchq.github.io/CyberChef) is the ultimate "Swiss Army Knife" for decoding data. If you don't know what encoding scheme was used, use CyberChef's **"Magic"** operation to auto-detect it.

---

### 2. Encryption (Confidentiality)

Encryption transforms readable data (plaintext) into an unreadable format (ciphertext) using a mathematical algorithm and a secret key. You cannot reverse it without the key.

#### Symmetric Encryption

* **Concept:** Uses the **same key** to both encrypt and decrypt the data.
* **Speed:** Very fast. Used for encrypting large amounts of data (e.g., your hard drive, database records).
* **Examples:** AES (Advanced Encryption Standard), DES (Outdated/Insecure), RC4.
* **CTF Attack Vector:** Finding the hardcoded key in an application's source code or memory to decrypt stolen data.

#### Asymmetric Encryption (Public Key Cryptography)

* **Concept:** Uses a mathematically linked **key pair** (a Public Key and a Private Key). Data encrypted with the Public Key can only be decrypted by the Private Key, and vice versa.
* **Speed:** Much slower. Used for securely exchanging symmetric keys or digital signatures (e.g., SSL/TLS handshakes, SSH authentication).
* **Examples:** RSA, ECC (Elliptic Curve Cryptography), PGP/GPG.
* **CTF Attack Vector:** Extracting poorly secured private keys (`id_rsa`) from compromised machines to access other servers.

#### Command Line Decryption (OpenSSL)

If you find an encrypted file and the corresponding symmetric key or password, you can decrypt it using `openssl`.

| Command | Description |
| --- | --- |
| `openssl enc -d -aes-256-cbc -in secret.enc -out plaintext.txt` | Decrypts an AES-256-CBC encrypted file. It will prompt for the password/key. |
| `openssl rsa -in private.key -text -noout` | Inspects an RSA private key to view its modulus and prime details. |

---

### 3. Hashing (Integrity & One-Way Verification)

Unlike encoding (which is reversible) and encryption (which is reversible with a key), hashing is a **one-way** mathematical function. It takes an input of any size and produces a fixed-length string of characters (a digest).

* **Purpose:** Verifying passwords (storing the hash, not the plaintext) and verifying file integrity (ensuring a downloaded file wasn't tampered with).
* **Examples:** MD5, SHA-1, SHA-256, bcrypt.
* **Reversibility:** Cannot be mathematically reversed. It must be cracked using brute-force or dictionary attacks (see `hash_cracking_hashcat.md`).

* *Developed by Wecncode Developer Community!🩶*
