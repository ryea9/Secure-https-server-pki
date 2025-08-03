# Secure HTTPS Server using a Self-Managed PKI

This project demonstrates the complete process of building a Public Key Infrastructure (PKI) from scratch to secure a web server using TLS/SSL. In this exercise, I acted as my own Root Certificate Authority (CA) to issue a valid X.509 certificate for a web server, providing a hands-on understanding of the chain of trust that secures modern internet communications.

---

### Core Concepts Demonstrated
*   **Public Key Infrastructure (PKI):** The framework of hardware, software, and policies used to manage digital certificates and public-key encryption.
*   **Certificate Authority (CA):** An entity that issues digital certificates. In this project, I created my own Root CA.
*   **X.509 Certificates:** The standard format for public-key certificates, containing a public key and identity information, all signed by a CA.
*   **Chain of Trust:** The hierarchical model where certificates are validated by tracing them back to a trusted Root CA.
*   **TLS/SSL:** The cryptographic protocols used to provide secure communication over a computer network.

### Tools Used
*   **OpenSSL:** The primary command-line tool used for all cryptographic operations.
*   **Ubuntu (Linux VM):** The operating environment for the server and CA.
*   **Mozilla Firefox:** Used as the client to verify the certificate and secure connection.

---

## Project Walkthrough

The project was executed in four distinct phases, from creating the trusted authority to deploying the secure server.

### Phase 1: Becoming a Root Certificate Authority (CA)
The first step was to create our own trusted authority. This CA's "root" certificate is the anchor of trust for any certificates it issues.

**1.1. Generate the CA's Private Key:**
This command creates a 2048-bit RSA private key, which will be used to sign all certificates issued by our CA. It is the most critical and sensitive file in the PKI.
```bash
openssl genrsa -out ca.key 2048
genrsa: Command to generate an RSA private key.
out ca.key: Specifies the output filename for the private key.
2048: The length of the key in bits.

**1.2. Create the CA's Self-Signed Root Certificate:
Using the private key, we generate a self-signed root certificate. This certificate contains the CA's public key and its identity information. Being self-signed, it forms the beginning of the chain of trust.

openssl req -new -x509 -days 365 -key ca.key -out ca.crt
req: Command for creating certificate requests and certificates.
-new: Prompts the user for the information needed in a certificate.
-x509: Specifies that we want to create a self-signed certificate, not a request (CSR).
-key ca.key: Uses our CA's private key to sign the certificate.

Phase 2: Generating a Certificate for the Web Server
The web server needs its own unique identity (key and certificate) to prove who it is to clients.
2.1. Generate the Server's Private Key:
Just like the CA, the server needs its own private key.
Generated bash
openssl genrsa -out server.key 2048
Use code with caution.
Bash
2.2. Create a Certificate Signing Request (CSR):
The server's public key and identity information are bundled into a CSR. This request will be sent to our CA to be officially signed. The "Common Name" (CN) here is critical—it must match the domain name of the server (e.g., utscrypto.com.au).
Generated bash
openssl req -new -key server.key -out server.csr
Use code with caution.
Bash
Phase 3: Signing the Server's Certificate
As the CA, we now process the server's CSR and sign it with our CA private key, officially issuing a valid certificate.
3.1. Issue the Server Certificate:
This command takes the server's CSR and validates it using our CA's key and certificate. The output is a signed server.crt file that is trusted by anyone who trusts our ca.crt.
Generated bash
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365
Use code with caution.
Bash
-in server.csr: Specifies the input CSR file.
-CA ca.crt: Specifies the CA's certificate to be used for signing.
-CAkey ca.key: Specifies the CA's private key to sign with.
-CAcreateserial: Generates a unique serial number for the certificate, which is a best practice.
Phase 4: Deploying and Testing the HTTPS Server
With the signed certificate, we can now run a secure web server.
4.1. Configure and Run the Server:
We combine the server's key and new certificate into a single .pem file and start a test server with OpenSSL.
Generated bash
# Combine the key and certificate into one file
cat server.key server.crt > server.pem

# Start the test HTTPS server
openssl s_server -cert server.pem -www```

**4.2. Verification:**
When first visiting the site (`https://utscrypto.com.au:4433`), the browser shows a "Potential Security Risk" error. This is expected because our self-made CA is not in the browser's default list of trusted authorities.

To complete the chain of trust, I manually imported the `ca.crt` file into the Firefox "Authorities" list. After re-visiting the page, the browser displayed the **lock icon (🔒)**, confirming that the server's certificate was successfully validated against our now-trusted Root CA.
-out ca.crt: Specifies the output filename for the certificate.
