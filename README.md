# gerrit
Gerrit configuration


## Problems:

when using SSH there is problem when adding ssh id_rsa key.


```
Jsch seems not to support the above private key format, to solve it, we can use ssh-keygen to convert the private key format to the RSA or pem mode, and the above program works again.

$ ssh-keygen -p -f ~/.ssh/id_rsa -m pem

```
