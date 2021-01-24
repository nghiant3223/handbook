# HMAC

## Definition

HMAC is a method to generate MAC using hash function together with a key. HMAC doesn't only provide collision resistance but also unforgeability. If you only share this key with trusted parties, given an HMAC signature, you can be confident that only one of the trusted parties could have generated that signature.

Due to common properties of hash functions, an HMAC is not as simple as hashing the data appended to the key. This construct is vulnerable to [length-extension](https://en.wikipedia.org/wiki/Length_extension_attack) attacks where an attacker can take a message and its hash signature, and use this to construct a longer message with a valid signature (thus breaking the guarantee of unforgeability).

## References

- [Whats the difference between MAC vs hash](https://security.stackexchange.com/a/1897)
- [What is the difference between a HMAC and a hash of data?](https://crypto.stackexchange.com/a/6494)
- [How is HMAC(message,key) more secure than Hash(key1+message+key2)](https://crypto.stackexchange.com/questions/15131/how-is-hmacmessage-key-more-secure-than-hashkey1messagekey2)