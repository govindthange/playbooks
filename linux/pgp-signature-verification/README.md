# How can the PGP signature for an executable downloaded from the internet be verified?

Lets say we downloaded [veracrypt](https://www.veracrypt.fr/en/Downloads.html)

## Step 1. Download the PGP Public Key.

PGP Public Key: https://www.idrix.fr/VeraCrypt/VeraCrypt_PGP_public_key.asc (ID=0x680D16DE, Fingerprint=5069A233D55A0EEB174A5FC3821ACD02680D16DE)

## Step 2. Note the Fingerprint of the PGP Public Key.

## Step 3. Import the publick key.

```
govind@thinkpad:~/Downloads$ gpg --import --import-options show-only VeraCrypt_PGP_public_key.asc 
gpg: key 821ACD02680D16DE: 1 signature not checked due to a missing key
pub   rsa4096 2018-09-11 [SC]
      5069A233D55A0EEB174A5FC3821ACD02680D16DE
uid                      VeraCrypt Team (2018 - Supersedes Key ID=0x54DDD393) <veracrypt@idrix.fr>
sub   rsa4096 2018-09-11 [E]
sub   rsa4096 2018-09-11 [A]

govind@thinkpad:~/Downloads$ gpg --import VeraCrypt_PGP_public_key.asc 
gpg: key 821ACD02680D16DE: 1 signature not checked due to a missing key
gpg: key 821ACD02680D16DE: "VeraCrypt Team (2018 - Supersedes Key ID=0x54DDD393) <veracrypt@idrix.fr>" not changed
gpg: Total number processed: 1
gpg:              unchanged: 1
```

## Step 4. Verify the .deb file with the .sig file.

```
govind@thinkpad:~/Downloads$ gpg --verify veracrypt-1.25.9-Ubuntu-22.04-amd64.deb.sig veracrypt-1.25.9-Ubuntu-22.04-amd64.deb
gpg: Signature made Thursday 19 May 2022 03:43:22 AM IST
gpg:                using RSA key 5069A233D55A0EEB174A5FC3821ACD02680D16DE
gpg: Good signature from "VeraCrypt Team (2018 - Supersedes Key ID=0x54DDD393) <veracrypt@idrix.fr>" [unknown]
gpg: WARNING: This key is not certified with a trusted signature!
gpg:          There is no indication that the signature belongs to the owner.
Primary key fingerprint: 5069 A233 D55A 0EEB 174A  5FC3 821A CD02 680D 16DE
```
