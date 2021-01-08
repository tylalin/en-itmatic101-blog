---
description: This post is showing how to use GPG key encryption on Linux environment.
---

# Using GPG to encrypt/decrypt files or messages

### Install:

apt-get install gnupg

### List keys:

gpg --list-secret-keys \# listing private keys  
gpg --list-keys \# listing public keys

### Generate keys:

gpg --full-generate-key   
OR   
gpg --gen-key

### Export/Import keys:

gpg --export -a tyla &gt; tyla\_public.key   
gpg --import tyla\_public.key   
gpg --export-secret-keys tyla &gt; tyla-private-key.key   
gpg --import tyla-private-key.key

### Encrypt & Decrypt

gpg --batch --passphrase pass -c msg.txt \# encrypt a file with passphrase  
gpg -d msg.txt.gpg \# decrypt the file with passphrase

gpg -e -r "tyla" msg.log   
gpg --always-trust -e -r "tyla" msg.log

gpg -d msg.log.gpg   
gpg --batch --passphrase pass msg.log.gpg

