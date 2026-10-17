# Decrypting Files with Linux: Caesar Cipher and OpenSSL

> **Disclaimer:** This project was completed as part of the Google Cybersecurity Certificate (Coursera). The scenario and files come from the course. The commands, outputs, and explanations are my own documentation of the work I performed.

## Project description
All of the files in my home directory had been encrypted, and I needed to recover the data using Linux commands. I explored the directory, found a hidden file protected by a Caesar cipher, decrypted it with the `tr` command to reveal the next instruction, and then used `openssl` to decrypt an AES-256 encrypted file and read its hidden message. This lab shows how encryption protects data and how weak and strong encryption differ in practice.

## Commands Used
| Command | Purpose |
|---|---|
| `ls` | List files in a directory |
| `ls -a` | List all files, including hidden files (names starting with a period) |
| `cat` | Display the contents of a file |
| `cd` | Change directory |
| `tr` | Translate characters from one set to another (used to break the Caesar cipher) |
| `openssl` | Encrypt and decrypt files with strong cryptographic algorithms |

## Read the contents of a file
I listed the files in my home directory and read the `README.txt` file for instructions:

```bash
ls
cat README.txt
```
Output:
```
Q1.encrypted  README.txt  caesar
```
```
Hello,
All of your data has been encrypted. To recover your data, you will need to solve a cipher. To get started look for a hidden file in the caesar subdirectory.
```

The README says the `caesar` subdirectory holds a hidden file with the next step.

## Find a hidden file
I moved into the `caesar` subdirectory and listed all files with `-a`, since hidden files don't appear in a normal `ls`:

```bash
cd caesar
ls -a
```
Output:
```
.  ..  .leftShift3
```

The hidden file is `.leftShift3`. Reading it shows scrambled text, because it was encrypted with a Caesar cipher:

```bash
cat .leftShift3
```
Output:
```
Lq rughu wr uhfryhu brxu ilohv brx zloo qhhg wr hqwhu wkh iroorzlqj frppdqg:

rshqvvo dhv-256-fef -sengi2 -d -g -lq T1.hqfubswhg -rxw T1.uhfryhuhg -n hwwxeuxwh
```

A Caesar cipher shifts each letter by a fixed number of places. The file name hints at the shift: `leftShift3` means each letter was shifted three places to the left, so `d` stands for `a`, `e` stands for `b`, and so on. To decrypt it, I piped the file into `tr`, which maps each shifted letter back to its original:

```bash
cat .leftShift3 | tr "d-za-cD-ZA-C" "a-zA-Z"
```
Output:
```
In order to recover your files you will need to enter the following command:

openssl aes-256-cbc -pbkdf2 -a -d -in Q1.encrypted -out Q1.recovered -k ettubrute
```

How the `tr` command works:
- The first set, `d-za-cD-ZA-C`, is the shifted alphabet (`d` through `z`, then `a` through `c`, for both lowercase and uppercase).
- The second set, `a-zA-Z`, is the normal alphabet.
- `tr` replaces each character in the first set with the character in the same position of the second set, which undoes the shift.

I then returned to my home directory:

```bash
cd ~
```

## Decrypt a file
The decrypted message gave me the exact command to recover the encrypted data file:

```bash
openssl aes-256-cbc -pbkdf2 -a -d -in Q1.encrypted -out Q1.recovered -k ettubrute
```

Breaking down the command:

| Part | Meaning |
|---|---|
| `openssl` | The tool used to run cryptographic operations |
| `aes-256-cbc` | The algorithm: AES with a 256-bit key in CBC mode, a secure symmetric cipher |
| `-pbkdf2` | Adds extra security when deriving the encryption key from the password |
| `-a` | Sets the encoding for the output (Base64) |
| `-d` | Decrypt, instead of encrypt |
| `-in Q1.encrypted` | The input file |
| `-out Q1.recovered` | The output file for the decrypted data |
| `-k ettubrute` | The password used to decrypt |

I listed the directory to confirm the new file, then read it:

```bash
ls
cat Q1.recovered
```
Output:
```
Q1.encrypted  Q1.recovered  README.txt  caesar
```
```
If you are able to read this, then you have successfully decrypted the classic cipher text. You recovered the encryption key that was used to encrypt this file. Great work!
```

## Full Command Sequence
```bash
ls
cat README.txt
cd caesar
ls -a
cat .leftShift3
cat .leftShift3 | tr "d-za-cD-ZA-C" "a-zA-Z"
cd ~
openssl aes-256-cbc -pbkdf2 -a -d -in Q1.encrypted -out Q1.recovered -k ettubrute
ls
cat Q1.recovered
```

## Summary
I used `ls -a` to find a hidden file, broke a Caesar cipher with `tr` to reveal the decryption command, and then used `openssl` with AES-256 to recover an encrypted file and read its message. The two ciphers in this lab show the range of encryption: the Caesar cipher was solved with a single command, while AES-256 can't be broken without the key. This is why modern systems rely on strong algorithms to protect sensitive data.

## Key Takeaways
- Hidden files start with a period and only show with `ls -a`.
- A Caesar cipher shifts every letter by a fixed amount, so it only has 25 possible shifts and is trivially easy to break. It is a historical cipher, not real protection.
- `tr` swaps one set of characters for another, which is enough to reverse a simple substitution like a Caesar shift.
- AES-256 is a symmetric cipher: the same key encrypts and decrypts, so protecting the key is what protects the data.
- Passing a password on the command line with `-k` is convenient in a lab, but in real use it can be exposed through shell history and process lists, so safer options such as a prompt or key file are preferred.
- Encryption protects the confidentiality of data, but it only works as well as the algorithm and the secrecy of the key.

## Tools
- Linux Bash shell
- `openssl`, `tr`, `ls`, `cat`, `cd`
