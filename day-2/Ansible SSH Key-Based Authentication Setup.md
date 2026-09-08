# Ansible SSH Key-Based Authentication Setup

This README documents how to configure **passwordless SSH key-based authentication** from an Ansible Controller to an Ubuntu EC2 managed node.

The setup uses:

* Ansible Controller: Ubuntu / WSL
* Managed Node: Ubuntu AWS EC2
* Initial AWS authentication: `.pem` key
* Ansible authentication: SSH key pair
* Remote user: `ubuntu`

---

## 1. Architecture

The final setup looks like this:

```text
                    Ansible Controller
                    Ubuntu / WSL
                         |
                         |
                 ansible_demo
                  PRIVATE KEY
                         |
                         | SSH
                         ↓
                 Ubuntu EC2 Server
                  Managed Node
                         |
                         |
                  authorized_keys
                         |
                         ↓
                 ansible_demo.pub
                    PUBLIC KEY
```

### Important

The private key stays on the Ansible Controller.

The public key is installed on the managed node.

```text
Controller:
    ansible_demo        → PRIVATE KEY

Managed Node:
    ~/.ssh/authorized_keys
        └── ansible_demo.pub
```

---

# 2. Two SSH Keys Used in This Setup

We used two different keys for two different purposes.

## `server.pem`

Location:

```bash
/home/ankith/server.pem
```

Purpose:

> Existing AWS EC2 key used for the initial login/access to the EC2 instance.

Example:

```bash
ssh -i /home/ankith/server.pem ubuntu@18.60.112.111
```

---

## `ansible_demo`

Location:

```bash
/home/ankith/.ssh/ansible_demo
```

This is the private key created for Ansible SSH authentication.

Public key:

```bash
/home/ankith/.ssh/ansible_demo.pub
```

Purpose:

> Future SSH/Ansible authentication.

---

# 3. Check the Keys

On the Ansible Controller:

```bash
cd ~/.ssh
ls -la
```

Expected:

```text
ansible_demo
ansible_demo.pub
known_hosts
```

Check the private key:

```bash
ls -l ~/.ssh/ansible_demo
```

Check the public key:

```bash
ls -l ~/.ssh/ansible_demo.pub
```

---

# 4. SSH Key Permissions

SSH is strict about private-key permissions.

The private key should normally be readable only by the owner.

Run:

```bash
chmod 600 ~/.ssh/ansible_demo
```

Check:

```bash
ls -l ~/.ssh/ansible_demo
```

Expected:

```text
-rw------- ... ansible_demo
```

### Meaning of `600`

```text
6 → owner: read + write
0 → group: no permissions
0 → others: no permissions
```

Therefore:

```text
Owner  → rw-
Group  → ---
Others → ---
```

This protects the private key.

---

# 5. Generate an Ansible SSH Key Pair

If the key does not already exist:

```bash
ssh-keygen -t ed25519 -f ~/.ssh/ansible_demo
```

This creates:

```text
~/.ssh/ansible_demo
~/.ssh/ansible_demo.pub
```

Where:

```text
ansible_demo
    ↓
PRIVATE KEY

ansible_demo.pub
    ↓
PUBLIC KEY
```

---

# 6. Copy the Public Key to the EC2 Server

The EC2 server already trusts the AWS `.pem` key.

We use that existing key to install the new Ansible public key.

Command:

```bash
ssh-copy-id -f -i ~/.ssh/ansible.pub -o IdentityFile=/home/ankith/server.pem ubuntu@98.130.129.74
```
```bash
ssh-copy-id -f -i {Location of public key ssh-keygen  --> ~/.ssh/ansible.pub} -o {Location of the keypair of ec2 -->  IdentityFile=/home/ankith/server.pem} ubuntu@98.130.129.74
```
### What this command does

```text
server.pem
    ↓
Used for initial authentication
    ↓
EC2 Server
    ↓
Install ansible_demo.pub
    ↓
~/.ssh/authorized_keys
```

In simple words:

> Use `server.pem` to access the EC2 server and install `ansible_demo.pub` into the Ubuntu user's authorized SSH keys.

---

# 7. First-Time SSH Host Verification

The first time connecting to a server, SSH may show:

```text
The authenticity of host '18.60.112.111' can't be established.

Are you sure you want to continue connecting
(yes/no/[fingerprint])?
```

This is normal.

If you have verified that the IP/server is the one you intend to connect to, type:

```text
yes
```

SSH will then save the host key in:

```text
~/.ssh/known_hosts
```

---

# 8. Important: `ssh-copy-id` Does Not Usually Create `ansible_demo.pub` on the Server

After copying the key, do NOT expect:

```text
/home/ubuntu/.ssh/ansible_demo.pub
```

Instead, the public key is normally added to:

```text
/home/ubuntu/.ssh/authorized_keys
```

So on the EC2 server:

```bash
cd ~/.ssh
ls -la
```

You should normally see:

```text
authorized_keys
```

Check its contents:

```bash
cat ~/.ssh/authorized_keys
```

It will contain one or more public SSH keys.

---

# 9. Test Ansible SSH Key Authentication

After installing the public key, exit the EC2 server if you're currently inside it:

```bash
exit
```

From the Ansible Controller run:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

If you successfully get:

```text
ubuntu@ip-172-31-14-57:~$
```

then SSH key authentication is working.

---

# 10. Important Difference Between Controller and Worker

Always remember:

## Controller

```text
/home/ankith/.ssh/

ansible_demo
    ↓
PRIVATE KEY

ansible_demo.pub
    ↓
PUBLIC KEY
```

## EC2 Managed Node

```text
/home/ubuntu/.ssh/

authorized_keys
    ↓
contains the public key
```

The private key should NOT be copied to the worker.

---

# 11. Common Problem: `Permission denied (publickey)`

Error:

```text
ubuntu@18.60.112.111: Permission denied (publickey).
```

Possible causes:

### Cause 1 — Wrong private key

Make sure you are using:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

and not accidentally using another key.

---

### Cause 2 — Private key permissions

Check:

```bash
ls -l ~/.ssh/ansible_demo
```

Fix:

```bash
chmod 600 ~/.ssh/ansible_demo
```

Then retry:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

---

### Cause 3 — Public key was not installed

Use the original AWS key to copy it again:

```bash
ssh-copy-id \
-f \
-i ~/.ssh/ansible_demo.pub \
-o IdentityFile=/home/ankith/server.pem \
ubuntu@18.60.112.111
```

The `-f` forces installation of the key.

Then test:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

---

# 12. Check the Public Key on the Worker

First connect using the working AWS key:

```bash
ssh -i /home/ankith/server.pem ubuntu@18.60.112.111
```

Then:

```bash
cd ~/.ssh
```

Check:

```bash
ls -la
```

Then:

```bash
cat ~/.ssh/authorized_keys
```

The `ansible_demo.pub` public key should be present there.

---

# 13. Verify the Key Fingerprint

On the Controller:

```bash
ssh-keygen -lf ~/.ssh/ansible_demo.pub
```

This displays the fingerprint of your public key.

On the worker:

```bash
ssh-keygen -lf ~/.ssh/authorized_keys
```

Compare the fingerprints.

This helps confirm that the expected key is installed.

---

# 14. Common Problem: `No identities found`

Error:

```text
/usr/bin/ssh-copy-id: ERROR: No identities found
```

This usually happens when `ssh-copy-id` doesn't know which public key you want to install.

Instead of:

```bash
ssh-copy-id ubuntu@18.60.112.111
```

explicitly specify the public key:

```bash
ssh-copy-id \
-i ~/.ssh/ansible_demo.pub \
-o IdentityFile=/home/ankith/server.pem \
ubuntu@18.60.112.111
```

The important part is:

```bash
-i ~/.ssh/ansible_demo.pub
```

---

# 15. Common Problem: Wrong PEM Path

Incorrect:

```text
~/home/ankith/server.pem
```

Why?

Because:

```text
~
```

already means:

```text
/home/ankith
```

Therefore:

```text
~/home/ankith/server.pem
```

would incorrectly point to:

```text
/home/ankith/home/ankith/server.pem
```

Correct:

```bash
/home/ankith/server.pem
```

or:

```bash
~/server.pem
```

---

# 16. Common Problem: `/.ssh` Does Not Exist

Do not use:

```bash
cd /.ssh
```

That means:

```text
/.ssh
```

at the root of the filesystem.

For the Ubuntu user, use:

```bash
cd ~/.ssh
```

or:

```bash
cd /home/ubuntu/.ssh
```

---

# 17. Common Problem: `ssh-copy-id` Says Key Already Exists

You may see:

```text
WARNING: All keys were skipped because they already exist on the remote system.
```

This means:

> The public key is already present on the remote server.

Usually this is not an error.

You can simply test:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

If it works, everything is configured correctly.

If you believe the wrong key is installed, force-copy it:

```bash
ssh-copy-id \
-f \
-i ~/.ssh/ansible_demo.pub \
-o IdentityFile=/home/ankith/server.pem \
ubuntu@18.60.112.111
```

---

# 18. Common Problem: Accidentally Using the Wrong Machine

Pay attention to your shell prompt.

Controller:

```text
ankith@BLRB1LT139:~$
```

Worker:

```text
ubuntu@ip-172-31-14-57:~$
```

If you see:

```text
ubuntu@ip-172-31-14-57:~$
```

you are inside the EC2 server.

If you need to return to the controller:

```bash
exit
```

---

# 19. Do Not Run This From the Worker

Do not expect this to work on the worker:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

because the private key:

```text
/home/ankith/.ssh/ansible_demo
```

belongs to the Controller.

It should remain on the Controller.

---

# 20. SSH Debugging

If normal SSH fails:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

use:

```bash
ssh -vvv -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

Look for lines such as:

```text
Offering public key
```

and:

```text
Server accepts key
```

or:

```text
Permission denied
```

This helps identify why SSH authentication is failing.

---

# 21. Do Not Share Private Keys

Never share the contents of:

```text
server.pem
ansible_demo
```

Do NOT run:

```bash
cat server.pem
```

or:

```bash
cat ~/.ssh/ansible_demo
```

and share the output.

Private keys are secrets.

Safe to inspect/share:

```bash
ls -l ~/.ssh/
```

```bash
ssh-keygen -lf ~/.ssh/ansible_demo.pub
```

SSH error messages and debugging output are generally okay, but remove any secrets if they appear.

---

# 22. Final Working Setup

The final architecture is:

```text
                 ANSIBLE CONTROLLER
                 /home/ankith
                       |
                       |
               ~/.ssh/ansible_demo
                  PRIVATE KEY
                       |
                       | SSH
                       ↓
              AWS EC2 / MANAGED NODE
              ubuntu@18.60.112.111
                       |
                       ↓
             /home/ubuntu/.ssh/
                       |
                       ↓
                authorized_keys
                       |
                       ↓
              ansible_demo.pub
                 PUBLIC KEY
```

Authentication:

```text
Private Key
     +
Public Key
     =
SSH Key-Based Authentication
```

This is commonly called:

```text
Passwordless SSH
```

It does NOT mean there is no authentication.

It means SSH authenticates using the key pair instead of asking for an interactive SSH password.

---

# 23. Quick Recovery Checklist

If SSH suddenly fails, check these in order:

```bash
# 1. Check private key
ls -l ~/.ssh/ansible_demo

# 2. Fix permissions
chmod 600 ~/.ssh/ansible_demo

# 3. Check public key
ls -l ~/.ssh/ansible_demo.pub

# 4. Test SSH
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111

# 5. If it fails, use verbose mode
ssh -vvv -i ~/.ssh/ansible_demo ubuntu@18.60.112.111

# 6. If the public key needs reinstalling
ssh-copy-id \
-f \
-i ~/.ssh/ansible_demo.pub \
-o IdentityFile=/home/ankith/server.pem \
ubuntu@18.60.112.111
```

---

# 24. Next Step: Ansible

Once this works:

```bash
ssh -i ~/.ssh/ansible_demo ubuntu@18.60.112.111
```

the SSH layer is complete.

Next we can configure Ansible:

```text
Ansible Controller
       |
       ↓
Inventory
       |
       ↓
SSH
       |
       ↓
ansible_demo
       |
       ↓
EC2 Managed Node
```

Then test:

```bash
ansible all -m ping
```

After that, learn:

1. Ansible inventory
2. `ansible_user`
3. SSH private key configuration
4. `ansible all -m ping`
5. Playbooks
6. `become` / sudo
7. Ansible Vault
8. Bastion / Jump Host
9. Jenkins + Ansible
10. AWX / Ansible Automation Platform
