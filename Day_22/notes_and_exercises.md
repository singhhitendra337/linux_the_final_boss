# Day 22: Certificate Management (SSL/TLS, OpenSSL, Certbot)

## Why Certificate Management Matters
- Secures data in transit, enables HTTPS, and builds trust for users.
- Essential for web servers, APIs, and secure communications.

## Key Concepts
- SSL/TLS basics: encryption, certificates, CA, chain of trust
- Self-signed vs. CA-signed certificates
- OpenSSL: generate, view, and manage certificates
- Certbot: automate Let's Encrypt certificates
- Certificate renewal and revocation

## Common Commands
- `openssl req`, `openssl x509`, `openssl genrsa`, `openssl s_client`, `certbot`, `systemctl reload nginx|apache2`, `update-ca-certificates`

## Practical Examples
### 1. Generate a Self-Signed Certificate
```bash
openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout mysite.key -out mysite.crt
```

### 2. Get a Free Let's Encrypt Certificate with Certbot
```bash
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d example.com
```

### 3. View Certificate Details
```bash
openssl x509 -in mysite.crt -text -noout
```

### 4. Renew Certificates
```bash
sudo certbot renew
```

## Best Practices
- Always use strong keys (2048+ bits for RSA).
- Automate renewals and monitor expiry.
- Protect private keys and restrict permissions.
- Test certificates with SSL Labs or `openssl s_client`.

## Exercises
1. Generate a self-signed certificate for a test domain.
2. Set up HTTPS on a local Nginx server using Certbot.
3. View and explain the details of a certificate file.
4. Automate certificate renewal and test expiry alerts.

## Solutions
### 1. Use `openssl req -x509 ...` to generate.
### 2. Use `certbot --nginx` or `--apache` for setup.
### 3. Use `openssl x509 -in ... -text -noout` to view details.
### 4. Use `certbot renew` and monitoring scripts for expiry.

## Interview-Style Questions & Answers
1. **What is the difference between SSL and TLS?**
   - TLS is the modern, more secure successor to SSL.
2. **How do you generate a CSR and private key?**
   - Use `openssl req -new -newkey rsa:2048 -nodes -keyout key.pem -out req.csr`.
3. **How do you automate certificate renewal?**
   - Use Certbot with cron/systemd timers.
4. **How do you check certificate expiry?**
   - Use `openssl x509 -enddate -noout -in cert.crt`.
5. **What is a certificate chain?**
   - The sequence of certificates from server to root CA, establishing trust.
