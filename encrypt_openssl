## SYMMETRIC ENCRYPTION

For symmetic encryption, you can use the following:

### To encrypt:
```sh
openssl aes-256-cbc -salt -a -e -in plaintext.txt -out encrypted.txt
```

### To decrypt:
```sh
openssl aes-256-cbc -salt -a -d -in encrypted.txt -out plaintext.txt
```

## ASYMMETRIC ENCRYPTION
For Asymmetric encryption you must first generate your private key and extract the public key.

```sh
openssl genrsa -aes256 -out private.key 8912
openssl -in private.key -pubout -out public.key
```

### To encrypt:
```sh
openssl rsautl -encrypt -pubin -inkey public.key -in plaintext.txt -out encrypted.txt
```

### To decrypt:
```sh
openssl rsautl -decrypt -inkey private.key -in encrypted.txt -out plaintext.txt
```
