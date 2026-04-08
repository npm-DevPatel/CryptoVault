# Credit Card Vault — Encryption Demonstration

A fully-functional, interactive educational web application demonstrating modern cryptographic practices for securing sensitive financial data. Built with HTML5, CSS3, and the Web Crypto API.

## 📋 Overview

This project showcases how to properly encrypt credit card data and hash passwords using industry-standard cryptographic algorithms. It's designed as an educational tool to help developers understand:

- **Application-layer encryption** (encrypt before data reaches the database)
- **AES-256-GCM encryption** for sensitive fields with authenticated encryption
- **Key management best practices** (keys stored separately from encrypted data)
- **Password hashing with bcrypt-style PBKDF2** for one-way password verification
- **The difference between encryption and hashing** and when to use each
- **Randomized IVs (Initialization Vectors)** to prevent pattern-matching attacks

## 🔐 Key Features

### 1. **AES-256-GCM Card Encryption**
- Encrypts credit card number, expiry date, and CVV independently
- Generates a fresh random 12-byte IV for each field
- Provides both **confidentiality** (hides data) and **authenticity** (detects tampering)
- Base64-encodes output for safe database text column storage

### 2. **Bcrypt-Style Password Hashing**
- Uses PBKDF2 with configurable cost factors (8-14)
- Each password hashes differently (random salt prevents rainbow tables)
- Intentionally slow (300ms at cost 12) to make brute-force attacks impractical
- Demonstrates why SHA-256 is insufficient for password storage

### 3. **Interactive Encryption Pipeline**
- Step-by-step visualization of the 6-stage encryption process
- Real-time animation showing what happens at each step
- Animated status indicators (waiting → processing → complete)
- Displays intermediate outputs: plaintext → key → IV → ciphertext → Base64 → verified

### 4. **Simulated MySQL Database**
- Shows how encrypted data appears in a production database
- Displays plaintext cardholder name and last-4 card mask (safe to show)
- Ciphertext for sensitive fields (card number, expiry, CVV) - completely unreadable
- "Decrypt" buttons prove data is fully recoverable with the correct key
- Includes SQL schema reference for the `CardDetails` table

### 5. **Algorithm Justification Tab**
- Explains why AES-256-GCM was chosen over alternatives (CBC, etc.)
- Explains why bcrypt is superior to SHA-256 for passwords
- Details key management strategy (keys on app server, never in database)
- Explains application-layer encryption vs. MySQL's built-in encryption
- Comparison table showing our choices vs. less secure alternatives

## 🚀 How It Works

### Card Encryption Process (6 Steps)

```
1️⃣  Read Plaintext Values
    ↓
    Raw card number, expiry, CVV are read from form inputs

2️⃣  Generate AES-256 Key
    ↓
    256-bit symmetric key created (2^256 possible keys)

3️⃣  Generate Fresh Random IV
    ↓
    12-byte random number generated for each field

4️⃣  AES-256-GCM Encryption
    ↓
    Each field encrypted independently → unreadable ciphertext

5️⃣  Base64 Encoding
    ↓
    IV + ciphertext combined and Base64-encoded for database storage

6️⃣  Verify Decryption
    ↓
    Decrypt to confirm reversibility (data saved to simulated database)
```

### Password Hashing Process

```
Plaintext Password
    ↓
Generate Random Salt (16 bytes)
    ↓
PBKDF2 (2^cost iterations, SHA-256)
    ↓
Bcrypt-format Hash (with embedded salt & cost)
    ↓
Store in Database (password never retrievable)

Verification Process:
Test Password → PBKDF2 (same salt/cost) → Compare hashes
```

## 🛡️ Security Principles

### 1. **Separation of Concerns**
- Encryption key lives on the app server (in production)
- Database contains only ciphertext
- An attacker must compromise both server AND database

### 2. **Randomized IVs**
- Same card number encrypted twice produces different ciphertext
- Prevents pattern-matching attacks
- IV is stored with ciphertext (it's not secret, only the key is secret)

### 3. **Authenticated Encryption (GCM Mode)**
- Detects if ciphertext has been tampered with
- Different from CBC mode which only provides confidentiality
- Decryption fails if data is modified

### 4. **One-Way Hashing for Passwords**
- Passwords are hashed, not encrypted
- Even the app server cannot recover original passwords
- Verification works by hashing the test password and comparing

### 5. **Intentionally Expensive Hashing**
- Cost factor makes each hash attempt take time (300ms at cost 12)
- Brute-force attacks become computationally infeasible
- Each cost increase doubles the time per attempt

## 📊 Design Decisions vs. Alternatives

| Decision | Our Choice | Alternative | Why Ours is Stronger |
|----------|-----------|-------------|----------------------|
| **Encryption Mode** | AES-256-GCM | AES-256-CBC | GCM adds authentication—detects tampering. CBC does not. |
| **Key Size** | 256-bit | 128-bit | Double the brute-force resistance. Required under PCI-DSS. |
| **IV Strategy** | Random per field | Fixed IV | Unique IVs prevent ciphertext pattern detection. |
| **Password Storage** | Bcrypt cost 12 | SHA-256 | 10 billion times slower per attempt. SHA-256 is crackable in seconds. |
| **Encryption Location** | App layer (before DB) | MySQL AES_ENCRYPT() | DB never sees plaintext. Key separated from data. |
| **Card Display** | Store last 4 digits | Decrypt on every display | No decryption needed for UI. Reduces key usage and exposure. |

## 💻 Technical Stack

- **HTML5**: Semantic structure with detailed inline comments
- **CSS3**: Custom properties (variables), flexbox, grid layouts
- **JavaScript**: Web Crypto API for all cryptographic operations
- **No Dependencies**: Works in modern browsers with zero external libraries

## 🔧 Browser Compatibility

Requires modern browsers supporting the Web Crypto API:
- ✅ Chrome/Edge 37+
- ✅ Firefox 34+
- ✅ Safari 11+
- ✅ Opera 24+
- ❌ Internet Explorer (not supported)

## 📝 Code Documentation

Every section of the code is thoroughly commented:

### CSS (~400 lines of comments)
- CSS custom properties explained
- Component styling rationale
- Animation descriptions
- Responsive design decisions

### HTML (~200 lines of comments)
- Section purposes
- Form field explanations
- Pipeline step descriptions
- Database schema documentation

### JavaScript (~500 lines of comments)
- Function purposes and security notes
- Encryption/decryption process breakdown
- Parameter descriptions
- Production vs. demo mode differences

## 🎓 Educational Value

This project is designed to teach:

1. **How banks store credit card data securely**
2. **Why encryption keys must be separated from encrypted data**
3. **What authenticated encryption means (GCM mode)**
4. **Why passwords should never be encrypted**
5. **How to use the Web Crypto API safely**
6. **Common cryptographic mistakes to avoid**

### PCI-DSS Alignment

While this is a demo, it follows PCI-DSS (Payment Card Industry Data Security Standard) principles:
- ✅ AES-256 encryption (minimum required)
- ✅ Authenticated encryption (GCM mode)
- ✅ Random IVs per encryption operation
- ✅ Key separation (not stored with data)
- ✅ Strong password hashing (bcrypt-style, cost-based)
- ✅ Sensitive fields encrypted before database storage

## 🚀 Getting Started

### Local Usage

1. Clone this repository
   ```bash
   git clone https://github.com/npm-DevPatel/CryptoVault.git
   cd CryptoVault
   ```

2. Open the HTML file in a web browser
   ```bash
   # Simply open CardVault_Demo.html in your browser
   # No build process, no dependencies, no installation needed
   ```

3. Explore the four demo tabs:
   - **Card Encryption**: Watch real-time encryption pipeline
   - **Password Hashing**: Explore password hashing mechanics
   - **Simulated Database**: See how encrypted data is stored
   - **Algorithm Info**: Understand the security choices

### Demo Features

- **Pre-filled Form**: Card details filled in by default; modify to test
- **Live Visualization**: See each step of encryption with real data
- **Database Simulation**: Stored records persist during session
- **Decryption Proof**: Click "Decrypt" to reveal original data
- **Clear Table**: Remove all records and start fresh

## ⚠️ Important Notes

### This is Educational Software

This demo is for **learning purposes only**. In a production system:

1. **Never generate keys in the browser** - keys should be on secure servers
2. **Never store keys in code or environment** - use Key Management Services (KMS)
3. **Use actual bcrypt libraries** - don't implement password hashing yourself
4. **Use HTTPS** - all data in transit must be encrypted
5. **Implement key rotation** - rotate encryption keys every 90 days
6. **Use TLS** - encrypt database connections as well
7. **Implement audit logging** - track who accessed encrypted data

### Production Recommendations

For production systems:

- Use established libraries: `bcryptjs` (Node.js), `bcrypt` (Python), `golang.org/x/crypto/bcrypt` (Go)
- Store encryption keys in AWS KMS, Azure Key Vault, or HashiCorp Vault
- Implement key rotation policies
- Use application-layer encryption (like this demo)
- Never encrypt passwords; only hash them
- Implement rate limiting on password verification
- Use TLS for all data in transit
- Log all key access and encryption operations

## 📚 Learning Resources

### Understanding the Concepts

- **AES Encryption**: https://en.wikipedia.org/wiki/Advanced_Encryption_Standard
- **GCM Mode**: https://en.wikipedia.org/wiki/Galois/Counter_Mode
- **Key Derivation (PBKDF2)**: https://en.wikipedia.org/wiki/PBKDF2
- **Bcrypt**: https://en.wikipedia.org/wiki/Bcrypt
- **Web Crypto API**: https://developer.mozilla.org/en-US/docs/Web/API/Web_Crypto_API

### PCI-DSS Standards

- https://www.pcisecuritystandards.org/

## 📄 File Structure

```
CryptoVault/
├── CardVault_Demo.html      # Main application (fully commented)
├── README.md                # This file
└── .gitignore              # Git ignore rules
```

## 👨‍💻 Author

**dev-patel** - APT3090 Cryptography Project
- USIU Course: Advanced Programming Techniques (APT3090)
- Semester 2, Year 3
- Date: 2024

## 📄 License

Educational use only. This code is provided as-is for learning purposes.

## 🤝 Contributing

This is an educational project. Feel free to:
- Fork and modify for your own learning
- Add additional crypto algorithms to explore
- Extend with new features (HMAC, digital signatures, etc.)
- Create variations with different UI frameworks

## ❓ FAQ

**Q: Can I use this in production?**  
A: No. This is educational software. See "Production Recommendations" above.

**Q: Why use PBKDF2 instead of actual bcrypt?**  
A: PBKDF2 is available in Web Crypto API (browser-safe). Real bcrypt should be on the server.

**Q: Why not use AES-CBC instead of GCM?**  
A: GCM provides authenticated encryption (detects tampering). CBC doesn't authenticate.

**Q: Where are the keys stored?**  
A: Nowhere permanently—they're generated on-the-fly in this demo. Production uses KMS.

**Q: Is it safe to encrypt card data in the browser?**  
A: No—this is only for demonstration. Use HTTPS + server-side encryption in production.

## 📞 Support

For questions about cryptography concepts, refer to the algorithm documentation or leave comments in the code.

---

**Last Updated**: April 2026  
**Version**: 1.0.0  
**Status**: Educational/Demo
