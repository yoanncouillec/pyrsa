# PyRsa

RSA (Rivest, Shamir and Adleman) is a cryptosystem based on
factorization of big prime numbers. It is a nice and interesting
application of modular arithmetic. It involves the Euler's totient
function and generates a pair of key, a public one and a private one.
We can see two main applications:

* **Signature:** Sign your messages with the private one and then
everybody can verify using the public key.
* **Encryption:** Encrypt with the public key and decrypt with the private key.

## PyRsa is a Python library to show you the entire implementation of the RSA cryptosystem.

* **Easy:** You can generate public/private keys, encrypt and decrypt in a few commands.
* **Secure:** The RSA algorithm is fully implemented avoiding security holes.
* **Clear:** The code is clear and easily readable, you can learn from it and make it totally yours.

## Examples

```python
from rsa import Rsa
rsa = new Rsa()
rsa.encrypt("public.key", "message.txt", "cypher.nop")
rsa.decrypt("private.key", "cypher.nop", "message.back")
import filecmp
assert(filecmp.cmp("message.txt", "message.back"))
```

This example will start a cluster with default configuration (1 config replica set, 2 data replica sets, with a replica factor of 3).

## Installation

The library is a Python module and can be imported directly in your code.

## License

MongoManager is made under the terms of the MIT license.