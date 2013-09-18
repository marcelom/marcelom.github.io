If you want the encryption to be platform independent, you can use openssl:

Encryption:

openssl aes-256-cbc -in attack-plan.txt -out message.enc

Decryption:

openssl aes-256-cbc -d -in message.enc -out plain-text.txt

You can get openssl to base64-encode the message by using the -a switch on both encryption and decryption. This way, you can paste the ciphertext in an email message, for example. It'll look like this:

stefano:~$ openssl aes-256-cbc -in attack-plan.txt -a
enter aes-256-cbc encryption password:
Verifying - enter aes-256-cbc encryption password:
U2FsdGVkX192dXI7yHGs/4Ed+xEC3ejXFINKO6Hufnc=
Note that you have a choice of ciphers and modes of operation. For normal use, I recommend aes 256 in CBC mode. These are the ciphers modes you have available (only counting AES):

aes-128-cbc ← this is okay
aes-128-ecb
aes-192-cbc
aes-192-ecb
aes-256-cbc ← this is recommended
aes-256-ecb
See also:

http://en.wikipedia.org/wiki/Symmetric-key_algorithm
http://en.wikipedia.org/wiki/Block_cipher_modes_of_operation
Please note:

OpenSSL will ask you for a password. This is not an encryption key, it is not limited to 32 bytes! If you're going to transfer files with someone else, your shared secret should be very strong. You can use this site to get a sense of how good your password is:

https://www.grc.com/haystack.htm (this doesn't take into account any dictionary attacks!)
http://howsecureismypassword.net (this at least check for common passwords)
