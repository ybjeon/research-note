# Markdown note
## Make Reference

### Style 1: Go to Reference anchor
#### code
```markdown
[Threat Modeling, OWASP](#OWASP_Threat_Modeling)  

## Reference
<a id="OWASP_Threat_Modeling"></a>
[1] [OWASP Threat Modeling](https://owasp.org/www-community/Threat_Modeling)  
```

#### result
lorem ipsum dolor sit amet. [[1](#OWASP_Threat_Modeling)]  
lorem ipsum dolor sit amet.<sup>[[1](#OWASP_Threat_Modeling)]</sup>

### Style 2: Go to Link
#### code
```markdown
lorem ipsum dolor sit amet. [[1][OWASP Threat Modeling]]  
lorem ipsum dolor sit amet.<sup>[[1][OWASP Threat Modeling]]</sup>  

## Reference
[OWASP Threat Modeling]: https://owasp.org/www-community/Threat_Modeling "Microsoft Threat Modeling Tool overview - Azure"
```
#### result
lorem ipsum dolor sit amet. [[1][OWASP Threat Modeling]]  
lorem ipsum dolor sit amet.<sup>[[1][OWASP Threat Modeling]]</sup>  



## Sample text

The quick brown fox jumps over the lazy dog. Pack my box with five dozen liquor jugs.

### Section: Background

Cryptography is the practice of securing communication by transforming readable data (plaintext) into an unreadable form (ciphertext). Modern cryptographic systems rely on mathematical problems that are computationally infeasible to solve without the correct key.

Key concepts include:
- **Symmetric encryption**: The same key is used for both encryption and decryption (e.g., AES).
- **Asymmetric encryption**: A public/private key pair is used (e.g., RSA, ECC).
- **Hash functions**: One-way functions that produce a fixed-length digest (e.g., SHA-256).

## Reference
<a id="OWASP_Threat_Modeling"></a>
[1] [OWASP Threat Modeling](https://owasp.org/www-community/Threat_Modeling)  
<a id="OWASP_Threat_Modeling_Process"></a>
[2] [OWASP Threat Modeling Process](https://owasp.org/www-community/Threat_Modeling_Process)  


<!-- Invisible below -->
[OWASP Threat Modeling]: https://owasp.org/www-community/Threat_Modeling "Microsoft Threat Modeling Tool overview - Azure"