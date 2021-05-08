# POP3(s) Dovecot
## pop3
- The web pages [10 - Web Service](10%20-%20Web%20Service.md) only mention about pop3, so that's it. But I tried all user names with the password invinciablehacker, not work......
```bash
└╼cn$ nc -v 192.168.20.7 55007
Connection to 192.168.20.7 port 55007 [tcp/*] succeeded!
+OK GoldenEye POP3 Electronic-Mail System
USER boris
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER natalya
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER admin
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER xenia
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER janus
+OK
PASS InvincibleHack3r
-ERR [AUTH] Authentication failed.
USER admin
+OK
PASS admin
-ERR [AUTH] Authentication failed.
USER admin
+OK
PASS p@ssword
-ERR [AUTH] Authentication failed.
^C
```

## pop3s
### sslyze
```bash
└╼cn$ python3 -m sslyze 192.168.20.7:55006

 CHECKING HOST(S) AVAILABILITY
 -----------------------------

   192.168.20.7:55006                     => 192.168.20.7




 SCAN RESULTS FOR 192.168.20.7:55006 - 192.168.20.7
 --------------------------------------------------

 * OpenSSL Heartbleed:
                                          OK - Not vulnerable to Heartbleed

 * Certificates Information:
       Hostname sent for SNI:             192.168.20.7
       Number of certificates detected:   1


     Certificate #0 ( _RSAPublicKey )
       SHA1 Fingerprint:                  9d6a92eb5f9fe9ba6cbddc9355fa5754219b0b77
       Common Name:                       localhost
       Issuer:                            localhost
       Serial Number:                     16593442279642505854
       Not Before:                        2018-04-24
       Not After:                         2028-04-23
       Public Key Algorithm:              _RSAPublicKey
       Signature Algorithm:               sha256
       Key Size:                          2048
       Exponent:                          65537
       DNS Subject Alternative Names:     []

     Certificate #0 - Trust
       Hostname Validation:               FAILED - Certificate does NOT match server hostname
       Android CA Store (9.0.0_r9):       FAILED - Certificate is NOT Trusted: self signed certificate
       Apple CA Store (iOS 14, iPadOS 14, macOS 11, watchOS 7, and tvOS 14):FAILED - Certificate is NOT Trusted: self signed certificate
       Java CA Store (jdk-13.0.2):        FAILED - Certificate is NOT Trusted: self signed certificate
       Mozilla CA Store (2021-01-24):     FAILED - Certificate is NOT Trusted: self signed certificate
       Windows CA Store (2021-02-08):     FAILED - Certificate is NOT Trusted: self signed certificate
       Symantec 2018 Deprecation:         ERROR - Could not build verified chain (certificate untrusted?)
       Received Chain:                    localhost
       Verified Chain:                    ERROR - Could not build verified chain (certificate untrusted?)
       Received Chain Contains Anchor:    ERROR - Could not build verified chain (certificate untrusted?)
       Received Chain Order:              OK - Order is valid
       Verified Chain contains SHA1:      ERROR - Could not build verified chain (certificate untrusted?)

     Certificate #0 - Extensions
       OCSP Must-Staple:                  NOT SUPPORTED - Extension not found
       Certificate Transparency:          NOT SUPPORTED - Extension not found

     Certificate #0 - OCSP Stapling
                                          NOT SUPPORTED - Server did not send back an OCSP response

 * TLS 1.3 Cipher Suites:
     Attempted to connect using 5 cipher suites; the server rejected all cipher suites.

 * ROBOT Attack:
                                          OK - Not vulnerable.

 * Session Renegotiation:
       Client Renegotiation DoS Attack:   OK - Not vulnerable
       Secure Renegotiation:              OK - Supported

 * Deflate Compression:
                                          OK - Compression disabled

 * TLS 1.0 Cipher Suites:
     Attempted to connect using 80 cipher suites.

     The server accepted the following 18 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * SSL 3.0 Cipher Suites:
     Attempted to connect using 80 cipher suites.

     The server accepted the following 18 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * OpenSSL CCS Injection:
                                          OK - Not vulnerable to OpenSSL CCS injection

 * Elliptic Curve Key Exchange:
       Supported curves:                  secp384r1
       Rejected curves:                   X25519, X448, prime192v1, prime256v1, secp160k1, secp160r1, secp160r2, secp192k1, secp224k1, secp224r1, secp256k1, secp521r1, sect163k1, sect163r1, sect163r2, sect193r1, sect193r2, sect233k1, sect233r1, sect239k1, sect283k1, sect283r1, sect409k1, sect409r1, sect571k1, sect571r1

 * SSL 2.0 Cipher Suites:
     Attempted to connect using 7 cipher suites; the server rejected all cipher suites.

 * TLS 1.2 Session Resumption Support:
      With Session IDs: NOT SUPPORTED (0 successful resumptions out of 5 attempts).
      With TLS Tickets: NOT SUPPORTED - Server returned a TLS ticket but then ignored it.

 * TLS 1.2 Cipher Suites:
     Attempted to connect using 156 cipher suites.

     The server accepted the following 30 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_GCM_SHA384                   256
        TLS_RSA_WITH_AES_256_CBC_SHA256                   256
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_GCM_SHA256                   128
        TLS_RSA_WITH_AES_128_CBC_SHA256                   128
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384             256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384             256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256             128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256             128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_GCM_SHA384               256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA256               256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_GCM_SHA256               128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA256               128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * TLS 1.1 Cipher Suites:
     Attempted to connect using 80 cipher suites.

     The server accepted the following 18 cipher suites:
        TLS_RSA_WITH_SEED_CBC_SHA                         128
        TLS_RSA_WITH_RC4_128_SHA                          128
        TLS_RSA_WITH_RC4_128_MD5                          128
        TLS_RSA_WITH_CAMELLIA_256_CBC_SHA                 256
        TLS_RSA_WITH_CAMELLIA_128_CBC_SHA                 128
        TLS_RSA_WITH_AES_256_CBC_SHA                      256
        TLS_RSA_WITH_AES_128_CBC_SHA                      128
        TLS_RSA_WITH_3DES_EDE_CBC_SHA                     168
        TLS_ECDHE_RSA_WITH_RC4_128_SHA                    128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA                256       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA                128       ECDH: secp384r1 (384 bits)
        TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA               168       ECDH: secp384r1 (384 bits)
        TLS_DHE_RSA_WITH_SEED_CBC_SHA                     128       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_256_CBC_SHA             256       DH (1024 bits)
        TLS_DHE_RSA_WITH_CAMELLIA_128_CBC_SHA             128       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_256_CBC_SHA                  256       DH (1024 bits)
        TLS_DHE_RSA_WITH_AES_128_CBC_SHA                  128       DH (1024 bits)
        TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA                 168       DH (1024 bits)

     The group of cipher suites supported by the server has the following properties:
       Forward Secrecy                    OK - Supported
       Legacy RC4 Algorithm               INSECURE - Supported


 * Downgrade Attacks:
       TLS_FALLBACK_SCSV:                 VULNERABLE - Signaling cipher suite not supported


 SCAN COMPLETED IN 22.48 S
 -------------------------
```


### sslscan
```bash
└╼cn$ sslscan 192.168.20.7:55006
Version: 2.0.9-static
OpenSSL 1.1.1k  25 Mar 2021

Connected to 192.168.20.7

Testing SSL server 192.168.20.7 on port 55006 using SNI name 192.168.20.7

  SSL/TLS Protocols:
SSLv2     disabled
SSLv3     enabled
TLSv1.0   enabled
TLSv1.1   enabled
TLSv1.2   enabled
TLSv1.3   disabled

  TLS Fallback SCSV:
Server does not support TLS Fallback SCSV

  TLS renegotiation:
Secure session renegotiation supported

  TLS Compression:
OpenSSL version does not support compression
Rebuild with zlib1g-dev package for zlib support

  Heartbleed:
TLSv1.2 not vulnerable to heartbleed
TLSv1.1 not vulnerable to heartbleed
TLSv1.0 not vulnerable to heartbleed

  Supported Server Cipher(s):
Preferred TLSv1.2  256 bits  ECDHE-RSA-AES256-GCM-SHA384   Curve P-384 DHE 384
Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-GCM-SHA384     DHE 1024 bits
Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-GCM-SHA256   Curve P-384 DHE 384
Accepted  TLSv1.2  128 bits  DHE-RSA-AES128-GCM-SHA256     DHE 1024 bits
Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-SHA384       Curve P-384 DHE 384
Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-SHA256         DHE 1024 bits
Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-SHA256       Curve P-384 DHE 384
Accepted  TLSv1.2  128 bits  DHE-RSA-AES128-SHA256         DHE 1024 bits
Accepted  TLSv1.2  256 bits  ECDHE-RSA-AES256-SHA          Curve P-384 DHE 384
Accepted  TLSv1.2  256 bits  DHE-RSA-AES256-SHA            DHE 1024 bits
Accepted  TLSv1.2  256 bits  DHE-RSA-CAMELLIA256-SHA       DHE 1024 bits
Accepted  TLSv1.2  128 bits  ECDHE-RSA-AES128-SHA          Curve P-384 DHE 384
Accepted  TLSv1.2  128 bits  DHE-RSA-AES128-SHA            DHE 1024 bits
Accepted  TLSv1.2  128 bits  DHE-RSA-SEED-SHA              DHE 1024 bits
Accepted  TLSv1.2  128 bits  DHE-RSA-CAMELLIA128-SHA       DHE 1024 bits
Accepted  TLSv1.2  256 bits  AES256-GCM-SHA384
Accepted  TLSv1.2  128 bits  AES128-GCM-SHA256
Accepted  TLSv1.2  256 bits  AES256-SHA256
Accepted  TLSv1.2  128 bits  AES128-SHA256
Accepted  TLSv1.2  256 bits  AES256-SHA
Accepted  TLSv1.2  256 bits  CAMELLIA256-SHA
Accepted  TLSv1.2  128 bits  AES128-SHA
Accepted  TLSv1.2  128 bits  SEED-SHA
Accepted  TLSv1.2  128 bits  CAMELLIA128-SHA
Accepted  TLSv1.2  128 bits  TLS_RSA_WITH_RC4_128_MD5
Accepted  TLSv1.2  128 bits  TLS_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.2  112 bits  TLS_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.2  112 bits  TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.2  128 bits  TLS_ECDHE_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.2  112 bits  TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA
Preferred TLSv1.1  256 bits  ECDHE-RSA-AES256-SHA          Curve P-384 DHE 384
Accepted  TLSv1.1  256 bits  DHE-RSA-AES256-SHA            DHE 1024 bits
Accepted  TLSv1.1  256 bits  DHE-RSA-CAMELLIA256-SHA       DHE 1024 bits
Accepted  TLSv1.1  128 bits  ECDHE-RSA-AES128-SHA          Curve P-384 DHE 384
Accepted  TLSv1.1  128 bits  DHE-RSA-AES128-SHA            DHE 1024 bits
Accepted  TLSv1.1  128 bits  DHE-RSA-SEED-SHA              DHE 1024 bits
Accepted  TLSv1.1  128 bits  DHE-RSA-CAMELLIA128-SHA       DHE 1024 bits
Accepted  TLSv1.1  256 bits  AES256-SHA
Accepted  TLSv1.1  256 bits  CAMELLIA256-SHA
Accepted  TLSv1.1  128 bits  AES128-SHA
Accepted  TLSv1.1  128 bits  SEED-SHA
Accepted  TLSv1.1  128 bits  CAMELLIA128-SHA
Accepted  TLSv1.1  128 bits  TLS_RSA_WITH_RC4_128_MD5
Accepted  TLSv1.1  128 bits  TLS_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.1  112 bits  TLS_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.1  112 bits  TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.1  128 bits  TLS_ECDHE_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.1  112 bits  TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA
Preferred TLSv1.0  256 bits  ECDHE-RSA-AES256-SHA          Curve P-384 DHE 384
Accepted  TLSv1.0  256 bits  DHE-RSA-AES256-SHA            DHE 1024 bits
Accepted  TLSv1.0  256 bits  DHE-RSA-CAMELLIA256-SHA       DHE 1024 bits
Accepted  TLSv1.0  128 bits  ECDHE-RSA-AES128-SHA          Curve P-384 DHE 384
Accepted  TLSv1.0  128 bits  DHE-RSA-AES128-SHA            DHE 1024 bits
Accepted  TLSv1.0  128 bits  DHE-RSA-SEED-SHA              DHE 1024 bits
Accepted  TLSv1.0  128 bits  DHE-RSA-CAMELLIA128-SHA       DHE 1024 bits
Accepted  TLSv1.0  256 bits  AES256-SHA
Accepted  TLSv1.0  256 bits  CAMELLIA256-SHA
Accepted  TLSv1.0  128 bits  AES128-SHA
Accepted  TLSv1.0  128 bits  SEED-SHA
Accepted  TLSv1.0  128 bits  CAMELLIA128-SHA
Accepted  TLSv1.0  128 bits  TLS_RSA_WITH_RC4_128_MD5
Accepted  TLSv1.0  128 bits  TLS_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.0  112 bits  TLS_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.0  112 bits  TLS_DHE_RSA_WITH_3DES_EDE_CBC_SHA
Accepted  TLSv1.0  128 bits  TLS_ECDHE_RSA_WITH_RC4_128_SHA
Accepted  TLSv1.0  112 bits  TLS_ECDHE_RSA_WITH_3DES_EDE_CBC_SHA

  Server Key Exchange Group(s):
TLSv1.2  192 bits  secp384r1 (NIST P-384)

  SSL Certificate:
Signature Algorithm: sha256WithRSAEncryption
RSA Key Strength:    2048

Subject:  localhost
Issuer:   localhost

Not valid before: Apr 24 03:23:52 2018 GMT
Not valid after:  Apr 23 03:23:52 2028 GMT
```

- What I actually need is login credential or the actual hostname, according to [10 - Web Service](10%20-%20Web%20Service.md), hostname should be GOLDENEYE.


