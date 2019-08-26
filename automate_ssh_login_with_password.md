1. Generate ssh key by
```bash
$ ssh-keygen -t rsa -b 2048
```
This command will create a folder .ssh and generate public key and private key named 'id_rsa.pub' and 'id_rsa' stored in the folder. 

2. Send the public key to the host server by:
```bash
$ ssh-copy-id username@hostname
```
After execute this command, it requires user to login the host server with password to verify identity.

3. Test it is successfuly set the ssh key with host server by:
```bash
$ ssh username@hostname
```
Successful setup will not ask for password.