---
description: This post is showing how to use GPG key encryption on Linux environment.
cover: >-
  https://images.unsplash.com/photo-1605351792643-fe0c43d18762?crop=entropy&cs=srgb&fm=jpg&ixid=M3wxOTcwMjR8MHwxfHNlYXJjaHw0fHxlbmNyeXB0fGVufDB8fHx8MTY5OTE3NzU1OHww&ixlib=rb-4.0.3&q=85
coverY: 0
---

# Using GPG to encrypt/decrypt files or messages

There are a few ways of encrypting files and email content. Most obvious reason to use GNU Privacy Guard (GnuPG) is free and popular choice among all cryptographic software suites available in the market plus its interoperability with any other OpenGPG implementations. Following is the basic stuff you need to know about GnuPG to be used in Linux environment.&#x20;

### Installing GnuPG with APT package manager

Most of the Debian/Ubuntu alike distro can run this command to install GnuPG on the system.&#x20;

```
$ apt install gnupg
```

### Listing keys

After installing GnuPG on the system, here is a couple of gpg commands you can run to list private key(s) and public key(s). For the first time running the commands, it won't list any key as expected.&#x20;

```
$ gpg --list-secret-keys # listing private keys
$ gpg --list-keys # listing public keys
```

### Generating key pair:

To generate a key pair, run the following commands

```
# Generating a key pair (full interactive)
$ gpg --full-generate-key 

# Generating a key pair (quick interactive)
$ gpg --gen-key
```

### Exporting/Importing keys:

Upon generating the key pair with gpg, now it is time to export the public key from the sender device and import it to the recipient device for encryption and decryption files and messages as below.

```
# Export the public key from sender
$ gpg --export -a tyla > tyla_public.key 

# Send the exported public key to recipient and import it to his/her device
$ gpg --import tyla_public.key 
```

If you like to share the private key with others for encryption/decryption among team, here is how to export and import the private key. Note that the team members you have shared the private key must be trustworthy and keep the private key securely for the security reasons. Also remember that **security is only as strong as the weakest link.**

```
$ gpg --export-secret-keys tyla > tyla-private-key.key 
$ gpg --import tyla-private-key.key
```

### Encrypt & Decrypt

After sharing the public/private keys as required, we can start encrypting files. First, let's encrypt a msg.txt file with passphrase in which we don't need to have the key pair generated but just a passphrase. It is a quite handy method where you just want to encrypt the file on your local computer or share the encrypted file with others. But you still have to find a way to share the passphrase with others as well. It is called a symmetric key encryption due to its nature of using the same key (same passphrase) to encrypt and decrypt the message as following.

```
# Encrypting a file with passphrase
$ gpg --batch --passphrase pass -c msg.txt 

# Decrypting it with the same passphrase (interactive)
$ gpg -d msg.txt.gpg 

# # Decrypting it with the same passphrase (non-interactive)
$ gpg --batch --passphrase pass -d msg.log.gpg
```

With private and public keys pair, it is called an asymmetric key encryption. Here is how to encrypt and decrypt the same msg.txt file with the key pair.&#x20;

```
# Encrypting a file with public key after importing it to recepient end (interactive)
$ gpg -e -r "tyla" msg.txt

# Encrypting a file with key pair (non-interactive)
$ gpg --always-trust -e -r "tyla" msg.txt

# Decrypting it after import the public 
gpg -d msg.txt.gpg
```
