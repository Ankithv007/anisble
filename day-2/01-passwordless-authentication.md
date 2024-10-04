# How to setup Passwordless Authentication

## EC2 Instances

### Using Public Key

```
ssh-copy-id -f "-o IdentityFile <PATH TO PEM FILE>" ubuntu@<INSTANCE-PUBLIC-IP>
```

- ssh-copy-id: This is the command used to copy your public key to a remote machine.
- -f: This flag forces the copying of keys, which can be useful if you have keys already set up and want to overwrite them.
- "-o IdentityFile <PATH TO PEM FILE>": This option specifies the identity file (private key) to use for the connection. The -o flag passes this option to the underlying ssh command.
- ubuntu@<INSTANCE-IP>: This is the username (ubuntu) and the IP address of the remote server you want to access.

`ubuntu@<INSTANCE-IP>`

-if it is not works directly then you public key is not attached to your ubuntu machine so do one thing connect your ubuntu machine by manually 
 `ssh -i <your key pair> ubuntu@<your ip address>`
- after login
- in your vm-------> go to `cd ~/.ssh ` you will get authorized key open that with vim editor
- in laptop-------> comeback to your laptop so this is your control node right go to  `cd ~/.ssh `there will be a public key private key and cofig key
- do not think about other key why do want right <smile ha > okkkk cat your public key that to your  cat id_rsa.pub copy your public key
- in your vm ------> go back to your vm or your ubuntu account and paste in  authorized key and save it that's it 

- try this once agin
 `ubuntu@<INSTANCE-IP>`
### Using Password 

- Go to the file `/etc/ssh/sshd_config.d/60-cloudimg-settings.conf`
- Update `PasswordAuthentication yes`
- Restart SSH -> `sudo systemctl restart ssh`
