# end-to-end encryption (E2EE)
## Definition
- A method of secure communication that `prevents third parties` from `accessing data` while `it's transferred` from one end system or device to another.
- Data is `encrypted` on the sender's system or device, and only the intended recipient can decrypt it.- Data cannot be read or tampered with by an internet service provider, application service provider, hacker or any other entity or service.
- Many popular messaging service providers use end-to-end encryption, including `Facebook`, `WhatsApp` and `Zoom`.
- It is harder for providers to share `user information` from their services with `authorities`.
- It helps `prevent` from potentially `leaking private message` to people involved in illegal activities.
## End-to-end encryption vs. encryption in transit
- In many messaging services, third parties store the data, which is `encrypted only in transit`:
    - Secures the data from unauthorized viewers only. 
    - The sender can view the information.
- `end-to-end encryption`:
    - `Encrypted data` is only `viewable` by those with `decryption keys`.
    - Prevents `unintended users`, including `third parties`, from `reading` or `modifying data`.
## Why end-to-end encryption is important?
- Is used when privacy is of the most concern.
- Privacy examples:
    - Include sensitive subjects such as business documents, financial details, legal proceedings, medical conditions or personal conversations. 
    - Consequently, failure to secure private data could result in `damages to enterprise businesses` and their customers.
- Can help secure data against `cyber attacks`. 
- These costs include `discovering` and `responding to the violation`, the cost of `downtime` and `lost revenue`, and the `long-term reputational damage to a business` and its brand. And in the case of compromised PII, it can lead to `a loss of customer trust`, regulatory fines, and even legal action.
- Can also allow `control to authorize user access` to `stored data`.
    - A centralized privileged user policy management system provides granular control over who has access to what information.
    - Coupled with a centralized key management system that adheres to key management interoperability protocol (KMIP), organizations can encrypt and protect data at every level.
## How end-to-end encryption is used ?
### Secure communications
- `Messaging` apps use end-to-end encryption to `keep conversations between its users private`.
- `Email` systems can be figured for E2EE, requires `Pretty Good Privacy (PGP) encryption` configuration.
### Password management
- `Password managers` like `1Password` protect a user's passwords. The user is on both endpoints and is the only person with a key.
### Data storage
- `Storage devices` often provide E2EE at rest.
## How end-to-end encryption works ?
- It can use Asymmetric or Symmetric encryption.
- Asymmetric, or public-key cryptography, encrypts and decrypts the data using two separate cryptographic keys.
- Symmetric encryption is a type of encryption where only one secret symmetric key is used to encrypt the plaintext and decrypt the ciphertext.
## Challenges with E2EE
### Endpoint security
- E2EE only encrypts data between the endpoints.
- The endpoints themselves are vulnerable to attack.
- Endpoint security protects data beyond in-transit.
### Man-in-the-Middle (MitM) Attack
- Hackers can impersonate the intended recipient, swap decryption keys and forward the message to the actual recipient without being detected.
### Backdoors
- Attackers can use `backdoor` to bypass encryption.
## Reference
- [What is end-to-end encryption?](https://www.ibm.com/topics/end-to-end-encryption)
							
							
