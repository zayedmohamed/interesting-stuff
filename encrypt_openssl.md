### SYMMETRIC ENCRYPTION

For symmetic encryption, you can use the following:

#### To encrypt:
```sh
openssl aes-256-cbc -salt -a -e -in plaintext.txt -out encrypted.txt
```

#### To decrypt:
```sh
openssl aes-256-cbc -salt -a -d -in encrypted.txt -out plaintext.txt
```

### ASYMMETRIC ENCRYPTION
For Asymmetric encryption you must first generate your private key and extract the public key.

```sh
openssl genrsa -aes256 -out private.key 8912
openssl rsa -in private.key -pubout -out public.key
```

#### To encrypt:
```sh
openssl rsautl -encrypt -pubin -inkey public.key -in plaintext.txt -out encrypted.txt
```

#### To decrypt:
```sh
openssl rsautl -decrypt -inkey private.key -in encrypted.txt -out plaintext.txt
```

### ENCRYPT LARGE FILE

You can't directly encrypt a large file using rsautl. instead, do something like the following:

- Generate a key using openssl rand, eg. openssl rand 32 -out keyfile
- Encrypt the key file using openssl rsautl
- Encrypt the data using openssl enc, using the generated key from step 1.
- Package the encrypted key file with the encrypted data. the recipient will need to decrypt the key with their private key, then   decrypt the data with the resulting key.

Ultimate solution for safe and high secured encode anyone file in OpenSSL and command-line:

You should have ready some X.509 certificate for encrypt files in PEM format.

NOTE: You can generated a X.509 certificate using:

#### Private key generation (encrypted private key):
```sh
openssl genrsa -aes256 -out private.pem 8912
openssl -in private.pem -pubout -out public.pem
```

#### With unecrypted private key:
```sh
openssl req -x509 -nodes -days 100000 -newkey rsa:8912 -keyout private_key.pem -out certificate.pem
```

#### With encrypted private key:
```sh
openssl req -x509 -days 100000 -newkey rsa:8912 -keyout private_key.pem -out certificate.pem
```

#### With existing encrypted (unecrypted) private key:
```sh
openssl req -x509 -new -days 100000 -key private_key.pem -out certificate.pem
```

#### To encrypt:
```sh
openssl smime -encrypt -binary -aes-256-cbc -in plainfile.zip -out encrypted.zip.enc -outform PEM yourSslCertificate.pem
openssl smime -encrypt -binary -aes-256-cbc -in plainfile.zip -out encrypted.zip.enc -outform DER yourSslCertificate.pem
```

#### For text files:
```sh
openssl smime -encrypt -aes-256-cbc -in input.txt -out output.txt -outform DER yourSslCertificate.pem
openssl smime -encrypt -aes-256-cbc -in input.txt -out output.txt -outform PEM yourSslCertificate.pem
```

#### What is what:

smime - ssl command for S/MIME utility (smime(1))
-encrypt - chosen method for file process
-binary - use safe file process. Normally the input message is converted to "canonical" format as required by the S/MIME specification, this switch disable it. It is necessary for all binary files (like a images, sounds, ZIP archives).
-aes-256-cbc - chosen cipher AES in 256 bit for encryption (strong). If not specified 40 bit RC2 is used (very weak). (Supported ciphers)
-in plainfile.zip - input file name
-out encrypted.zip.enc - output file name
-outform DER - encode output file as binary. If is not specified, file is encoded by base64 and file size will be increased by 30%.
yourSslCertificate.pem - file name of your certificate's. That should be in PEM format.
That command can very effectively a strongly encrypt any file regardless of its size or format.

#### To decrypt:
```sh
openssl smime -decrypt -binary -in encrypted.zip.enc -inform DER -out decrypted.zip -inkey private.key -passin pass:your_password
openssl smime -decrypt -binary -in encrypted.zip.enc -inform PEM -out decrypted.zip -inkey private.key -passin pass:your_password
```
#### For text files:
```sh
openssl smime -decrypt -in encrypted_input.txt -inform DER -out decrypted_input.zip -inkey private.key -passin pass:your_password
openssl smime -decrypt -in encrypted_input.txt -inform PEM -out decrypted_input.zip -inkey private.key -passin pass:your_password
```

#### What is what:

-inform DER - same as -outform above
-inkey private.key - file name of your private key. That should be in PEM format and can be encrypted by password.
-passin pass:your_password - your password for private key encrypt. (http://www.openssl.org/docs/apps/openssl.html#PASS_PHRASE_ARGUMENTS)

#### Generating public key from private key:
```sh
openssl rsa -in private_key.pem -pubout > public_key.pem
```

#### Generating public key from a certificate :
```sh
openssl x509 -inform pem -in certificate.crt -pubkey -noout > public_key.pem
```

#### Creating a signed digest of a file:
```sh
openssl dgst -sha512 -sign private_key.pem -out digest.sha512 file.txt
```

#### Verify a signed digest:
```sh
openssl dgst -sha512 -verify public_key.pem -signature digest.sha512 file.txt
```

Source: http://stackoverflow.com/questions/7143514/how-to-encrypt-a-large-file-in-openssl-using-public-key
http://www.madboa.com/geek/openssl/
http://stackoverflow.com/questions/5140425/openssl-command-line-to-verify-the-signature
