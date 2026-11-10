# Cryptography Standard: Protecting Data at Rest and in Transit

*Sample documentation by Kumar Gaurav. Example Corp is a fictional organisation created for portfolio purposes. Check all requirements against current standards before real use.*

| Field | Value |
| --- | --- |
| Document ID | SEC-STD-007 |
| Version | 1.2 |
| Owner | Head of Information Security |
| Applies to | All systems that store, process or transmit Example Corp data |
| Review cycle | Every 12 months |

## Purpose and scope

This standard sets the minimum cryptographic controls that protect Example Corp data. It applies to employees, contractors and suppliers who build, operate or buy systems that handle confidential or personal information. It covers encryption at rest, encryption in transit, key management, certificate management and the response to a suspected key compromise.

## Roles and responsibilities

| Role | Responsibility |
| --- | --- |
| Information Security | Owns this standard, approves algorithms and reviews exceptions. |
| System owners | Apply the controls to their systems and keep evidence of compliance. |
| Platform team | Operates the key management service and certificate authority. |
| Developers | Use approved libraries and never create custom cryptography. |

## Approved algorithms

| Purpose | Approved | Not permitted |
| --- | --- | --- |
| Symmetric encryption | AES-256-GCM, ChaCha20-Poly1305 | DES, 3DES, RC4, AES in ECB mode |
| Hashing | SHA-256, SHA-384, SHA-512 | MD5, SHA-1 |
| Password storage | Argon2id, bcrypt, scrypt | Unsalted or fast hashes |
| Digital signatures | ECDSA (P-256 or P-384), RSA 3072-bit or longer | RSA 2048-bit for new systems, DSA |
| Key exchange | ECDH (P-256 or P-384), TLS 1.3 key exchange | Static Diffie-Hellman with weak groups |

> **Rule:** Developers must use well-reviewed libraries. Writing your own cryptographic algorithms or protocols is prohibited.

## Encryption in transit

All network traffic that carries confidential data must meet these requirements:

1. Use **TLS 1.3** where supported. TLS 1.2 is the minimum. TLS 1.1 and earlier are prohibited.
2. Use cipher suites that provide forward secrecy, such as ECDHE with AES-GCM.
3. Use certificates issued by the Example Corp certificate authority or a trusted public authority.
4. Enable HSTS on public websites.
5. Use mutual TLS between internal services that handle personal or financial data.

## Encryption at rest

- Encrypt databases, file stores, backups and laptops by using AES-256.
- Use **envelope encryption** for application data: encrypt with a unique data key, and protect that key with a master key in the key management service.
- Store master keys only in the approved key management service or a hardware security module. Never store them in source code, configuration files or chat messages.
- Encrypt backups with keys that are separate from the keys that protect production data.

## Key management lifecycle

1. **Generate** keys inside the key management service by using a certified random source.
2. **Store** keys in the service or hardware security module. Restrict access by role.
3. **Distribute** keys only in wrapped (encrypted) form.
4. **Rotate** master keys at least every 12 months, and immediately after any suspected exposure.
5. **Retire** keys by disabling them, and keep them available for decryption until all dependent data is re-protected.
6. **Destroy** keys only after the retention period ends and the data owner approves in writing.

Every key operation must be logged. Logs must never contain key material or decrypted data.

## Certificate management

- Public certificates must be valid for no more than 398 days. Internal certificates should be valid for 12 months or less.
- Automate renewal wherever possible. Alert owners 30 days before expiry.
- Keep a central inventory of every certificate, its owner and its expiry date.
- Revoke a certificate immediately if its private key is exposed.

## Post-quantum readiness

Quantum computers could break today's public-key algorithms such as RSA and elliptic-curve cryptography. NIST has published standards for quantum-resistant algorithms, including ML-KEM for key establishment and ML-DSA for signatures. Australian Signals Directorate guidance sets 2030 as the horizon for moving away from traditional asymmetric algorithms, so confirm the current dates in the Information Security Manual.

System owners must:

- List every system that relies on RSA or elliptic-curve cryptography.
- Prioritise systems that protect data which must stay confidential for 10 years or more.
- Use crypto-agile designs, so algorithms can be replaced without redesigning the application.

## Procedure: suspected key compromise

Use this procedure if a key, token or certificate private key may have been exposed.

1. **Report** the incident to the Security Operations team immediately. Include the key name and where it may have been exposed.
2. **Contain** the risk by disabling the affected key or revoking the certificate.
3. **Assess** which systems and data used the key. Use key management logs to list recent operations.
4. **Rotate** the key and re-wrap or re-encrypt the affected data.
5. **Review** logs for unauthorised use between the possible exposure and containment.
6. **Notify** the privacy officer if personal information may have been accessed, so they can assess legal reporting duties.
7. **Record** the cause, actions and lessons learned in the incident register within five working days.

## Compliance mapping

| Requirement | Where this standard helps |
| --- | --- |
| ISO/IEC 27001:2022, Annex A control 8.24 (Use of cryptography) | Approved algorithms, key lifecycle and certificate rules |
| Australian Privacy Principle 11 (security of personal information) | Encryption at rest and in transit, and the compromise procedure |
| PCI DSS v4.0, Requirements 3 and 4 | Protection of stored and transmitted payment data |
| ASD Information Security Manual, cryptography guidelines | Algorithm choices and post-quantum planning |

## Exceptions and review

Request an exception through Information Security, and include the business reason, the risk, compensating controls and an end date. Exceptions last no more than six months. Information Security reviews this standard every 12 months, or sooner if a major algorithm weakness is found.
