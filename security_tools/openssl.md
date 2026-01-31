# OpenSSL Cheatsheet

---

## Basics
```bash
openssl version
openssl help
openssl list -commands
```

## Random Data
```bash
openssl rand 16            # 16 random bytes (binary)
openssl rand -hex 32       # 32 random bytes, hex encoded
openssl rand -base64 32
```

## Hashing / Digests
```bash
openssl dgst -sha256 file.txt
openssl dgst -sha512 file.txt
openssl dgst -sha256 < file.txt
```

### Verify Hash
```bash
openssl dgst -sha256 file.txt | diff - expected.sha256
```

## Symmetric Encryption

### Encrypt (AES-256)
```bash
openssl enc -aes-256-cbc -salt -in file.txt -out file.txt.enc
```

### Decrypt
```bash
openssl enc -d -aes-256-cbc -in file.txt.enc -out file.txt
```

### Stronger KDF (IMPORTANT)
```bash
openssl enc -aes-256-gcm -pbkdf2 -iter 100000 -salt -in file.txt -out file.enc
```

## Base64 Encoding
```bash
openssl base64 -in file.bin -out file.b64
openssl base64 -d -in file.b64 -out file.bin
```

## Key Generation

### RSA (Legacy / Compatibility)
```bash
openssl genrsa -out rsa.key 4096
```

### EC (Recommended)
```bash
openssl ecparam -name prime256v1 -genkey -noout -out ec.key
```

### View Key
```bash
openssl pkey -in ec.key -text -noout
```

## Certificate Signing Requests (CSR)

### Generate Key + CSR
```bash
openssl req -new -newkey rsa:4096 -nodes -keyout key.pem -out req.csr
```

### CSR with Existing Key
```bash
openssl req -new -key key.pem -out req.csr
```

### Inspect CSR
```bash
openssl req -in req.csr -text -noout
```

## Self-Signed Certificates
```bash
openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365 -nodes
```

## Certificates: Inspection
```bash
openssl x509 -in cert.pem -text -noout
openssl x509 -in cert.pem -fingerprint -sha256
openssl x509 -in cert.pem -enddate -noout
```

## Verify Certificate Chain
```bash
openssl verify cert.pem
openssl verify -CAfile ca.pem cert.pem
```

## PEM / DER Conversion
```bash
openssl x509 -in cert.pem -outform der -out cert.der
openssl x509 -in cert.der -inform der -out cert.pem
```

## Private Key Formats
```bash
openssl pkey -in key.pem -out key-unencrypted.pem
openssl pkey -aes256 -in key.pem -out key-encrypted.pem
```

## PKCS#12 (PFX)

### Create
```bash
openssl pkcs12 -export -out bundle.p12 -inkey key.pem -in cert.pem
```

### Extract
```bash
openssl pkcs12 -in bundle.p12 -nodes -out extracted.pem
```

## TLS Client

### Inspect HTTPS Certificate
```bash
openssl s_client -connect example.com:443
openssl s_client -connect example.com:443 -servername example.com
```

### Show Full Chain
```bash
openssl s_client -connect example.com:443 -showcerts
```

## TLS Server (Testing Only)
```bash
openssl s_server -key key.pem -cert cert.pem -accept 8443
```

## Diffie-Hellman Params
```bash
openssl dhparam -out dhparam.pem 2048
```

## Check Modulus (RSA)
```bash
openssl rsa -noout -modulus -in key.pem | openssl md5
openssl x509 -noout -modulus -in cert.pem | openssl md5
```

## Passwords (NOT RECOMMENDED FOR NEW SYSTEMS)
```bash
openssl passwd -6
openssl passwd -6 password
```

## Performance Testing
```bash
openssl speed aes sha256 rsa
```

## Common Pitfalls (Read This)
- `openssl enc` without `-pbkdf2` is weak → always use it
- AES-GCM preferred over CBC when possible
- Self-signed certs are NOT trusted
- OpenSSL errors are cryptic by design

## Files & Permissions
```bash
chmod 600 *.key
chmod 644 *.pem
```

## Docs
```bash
man openssl
man openssl-enc
man openssl-req
man openssl-x509
```

