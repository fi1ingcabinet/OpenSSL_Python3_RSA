# OpenSSL_Python3_RSA

This is explained here: https://cryptobloggins.com/?p=236

## 0. Versions:

### 0.1. Create plaintext:
```shell
echo "The quick brown fox jumped over the lazy dogs." > plaintext.txt 
cat plaintext.txt
The quick brown fox jumped over the lazy dogs.
```
### 0.2. OpenSSL
```shell
$ openssl version
LibreSSL 2.6.5
```
### 0.3. Python
```shell
$ python3 --version
Python 3.7.6
(venv) $ pip freeze
pycrypto==2.6.1
```

## 1. OpenSSL:

### 1.1. Gen priv key
```shell
openssl genrsa -out openSSLPrivKey.pem 2048
```
### 1.2. Gen pub key
```shell
openssl rsa -in openSSLPrivKey.pem -pubout -out openSSLPubkey.pem
```
### 1.3. Encrypt
```shell
openssl rsautl -encrypt -inkey openSSLPubkey.pem -pubin -in plaintext.txt -out openSSLCiphertext.bin
```
### 1.4. Decrypt
```shell
openssl rsautl -decrypt -inkey openSSLPrivKey.pem -in openSSLCiphertext.bin -out plaintext.decr.txt
```
### 1.5. Encode/Decode base64
```shell
openssl base64 -in openSSLCiphertext.bin -out openSSLCiphertext.base64
openssl base64 -d -in ciphertext.base64 -out ciphertext.base64.decode
```

## 2. Python:

### 2.1. Gen priv key
```python
from Crypto.PublicKey import RSA
key = RSA.generate(2048)
privKeyFile = open('pythonPrivKey.pem','wb')
privKeyFile.write(key.exportKey('PEM'))
privKeyFile.close()
```
### 2.2. Gen pub key
```python
from Crypto.PublicKey import RSA
privKeyFile = open('pythonPrivKey.pem','rb')
privKey = RSA.importKey(privKeyFile.read())
publicKey = privKey.publickey()
pubKeyFile = open('pythonPubKey.pem','wb')
pubKeyFile.write(publicKey.exportKey('PEM'))
pubKeyFile.close()
```
### 2.3. Encrypt
```python
from Crypto.Cipher import PKCS1_v1_5
from Crypto.PublicKey import RSA
from Crypto.Hash import SHA
message = open('plaintext.txt', 'rb').read()
key = RSA.importKey(open('pythonPubKey.pem').read())
cipher = PKCS1_v1_5.new(key)
ciphertext = cipher.encrypt(message)
ciphertext_file = open('pythonCiphertext.bin','wb')
ciphertext_file.write(ciphertext)
ciphertext_file.close()
```
### 2.4. Decrypt
```python
from Crypto.Hash import SHA
from Crypto import Random
from Crypto.Cipher import PKCS1_v1_5
from Crypto.PublicKey import RSA
key = RSA.importKey(open('openSSLPrivKey.pem').read())
dsize = SHA.digest_size
sentinel = Random.new().read(15+dsize)
cipher = PKCS1_v1_5.new(key)
ciphertext_file = open('openSSLCiphertext.bin','rb')
ciphertext = ciphertext_file.read()
message = cipher.decrypt(ciphertext, sentinel)
message
b'The quick brown fox jumped over the lazy dogs.\n'
```
### 2.5. Encode/Decode base64
```python
import base64
ciphertext_file = open('pythonCiphertext.bin','rb')
ciphertext = ciphertext_file.read()
ciphertext_base64 = base64.b64encode(ciphertext)
```
```python
import base64
ciphertext_file = open('ciphertext.base64','r')
ciphertext = ciphertext_file.read()
ciphertext_bin = base64.b64decode(ciphertext)
ciphertext_bin
```
