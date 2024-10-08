## Generate a Secure Password with base 64 encoded this key is too imp so palce it carefully and do not share with anyone this is the "master key"
- how to create
```
openssl rand -base64 2048 > vault.pass
```
- openssl:
This is a command-line tool that provides a variety of cryptographic operations, including the generation of random data, encryption, decryption, and certificate management.
- rand:
This is a subcommand of openssl that generates pseudo-random bytes. In this context, it’s used to create random data.
- -base64:
This flag specifies that the output should be encoded in base64 format. Base64 encoding is a way of converting binary data into an ASCII string using a specific character set. It’s useful for encoding binary data so it can be easily stored or transmitted over text-based protocols.
- 2048:
This parameter indicates the number of random bytes to generate. In this case, 2048 bytes of random data will be generated. When encoded in base64, this will result in a longer string (approximately 2736 characters), as base64 encoding expands the size of the data.
-  >:This is a redirection operator in the shell that directs the output of the command to a file instead of displaying it in the terminal.
- vault.pass:
This is the name of the file where the generated random base64 string will be saved. In this case, it’s being used as a password file for Ansible Vault.

   - This command is typically used to generate a secure, random password that can be used to encrypt sensitive files with Ansible Vault. By saving the generated password in vault.pass, you can easily reference this file later when creating or accessing encrypted files.

## create an encrypted file using Ansible Vault
- how to create
```
ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass
```
- if it is in different path
```
ansible-vault create group_vars/all/pass.yml --vault-password-file ../../vault.pass
```
```
ansible-vault create my_secret.yml --vault-password-file vault.pass
```

✅ ansible-vault create:
- This is an Ansible Vault command used to create a new encrypted file.
- When you use this command, Ansible Vault opens a text editor (usually vim or the editor set in your environment) where you can enter the content that you want to encrypt. Once you save and exit the editor, the content is saved and encrypted in the specified file.

✅ group_vars/all/pass.yml:
- This specifies the path and name of the file that you want to create and encrypt.
- group_vars/ is a special directory in Ansible where you can define variables that apply to specific groups of hosts.
- all/ is a subdirectory that means the variables inside it apply to all hosts in the inventory.

✅pass.yml is the file being created. This file will be encrypted using Ansible Vault.

✅--vault-password-file vault.pass:
- This flag specifies the path to a file (vault.pass) that contains the password used to encrypt/decrypt the file (pass.yml).
- Using --vault-password-file helps you automate the encryption and decryption process without needing to enter the password interactively each time. This is useful for running playbooks or tasks in an automated pipeline.

### where to store the vault 
1. aws secrete manager (parameter store)
2. hasicorp valut service

## additinally you can do different thing in  encrypted file in Ansible Vault
1.create
2.edit
3.encrypt
4.decrypt
5.view
```
ansible-vault <just change here  like create view edit> my_secret.yml --vault-password-file vault.pass

```
