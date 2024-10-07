## go and check ec2 folder for all project 
# Ansible Realtime project this the task

## Task 1

Create three(3) EC2 instances on AWS using Ansible loops
- 2 Instances with Ubuntu Distribution
- 1 Instance with Centos Distribution

Hint: Use `connection: local` on Ansible Control node.

## Task 2

Set up passwordless authentication between Ansible control node and newly created 
instances.

## Task 3

Automate the shutdown of Ubuntu Instances only using Ansible Conditionals

Hint: Use `when` condition on ansible `gather_facts`

## type-1 (but is not standers )
```

---
- hosts: localhost
  connection: local

  tasks:
  - name: Create EC2 instances
    amazon.aws.ec2_instance:
      name: "{{ item.name }}" ### here change the name
      key_name: "key_pair"
      instance_type: t2.micro
      security_group: default
      region: ap-south-1
      aws_access_key: "{{ec2_access_key}}"  # From vault as defined
      aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
      network:
        assign_public_ip: true
      image_id: "{{ item.image }}"   ### update image id of ubuntu
      tags:
        environment: "{{ item.name }}"
- name: Create EC2 instances
  amazon.aws.ec2_instance:
    name: "{{ item.name }}"   ### here change the name
    key_name: "key_pair"
    instance_type: t2.micro
    security_group: default
    region: ap-south-1
    aws_access_key: "{{ec2_access_key}}"  # From vault as defined
    aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
    network:
      assign_public_ip: true
    image_id: "{{ item.image }}" ### update image id of ubuntu
    tags:
      environment: "{{ item.name }}"      
- name: Create EC2 instances
  amazon.aws.ec2_instance:
    name: "{{ item.name }}"  ### here change the name
    key_name: "key_pair"
    instance_type: t2.micro
    security_group: default
    region: ap-south-1
    aws_access_key: "{{ec2_access_key}}"  # From vault as defined
    aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
    network:
      assign_public_ip: true
    image_id: "{{ item.image }}" ### update image id of amazon
    tags:
      environment: "{{ item.name }}"
```

### type-2 loops (only 2 insatnce will run because ansible idempotent in nature
```
---
- hosts: localhost
  connection: local

  tasks:
  - name: Create EC2 instances
    amazon.aws.ec2_instance:
      name: "mg-1"
      key_name: "key_pair"
      instance_type: t2.micro
      security_group: default
      region: ap-south-1
      aws_access_key: "{{ec2_access_key}}"  # From vault as defined
      aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
      network:
        assign_public_ip: true
      image_id: "{{ item}}"
    loop:
      - "ami-078264b8ba71bc45e"  # Update AMI ID according 
      - "ami-0dee22c13ea7a9a67"  # to your account
      - "ami-0dee22c13ea7a9a67"
```
### type 3 final one you can create role and add or direct wirte with yaml file your wish
```
---
- hosts: localhost
  connection: local

  tasks:
  - name: Create EC2 instances
    amazon.aws.ec2_instance:
      name: "{{ item.name }}"
      key_name: "key_pair"
      instance_type: t2.micro
      security_group: default
      region: ap-south-1
      aws_access_key: "{{ec2_access_key}}"  # From vault as defined
      aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
      network:
        assign_public_ip: true
      image_id: "{{ item.image }}"
      tags:
        environment: "{{ item.name }}"
    loop:
      - { image: "ami-078264b8ba71bc45e", name: "manage-node-1" } # Update AMI ID according 
      - { image: "ami-0dee22c13ea7a9a67", name: "manage-node-2" } # to your account
      - { image: "ami-0dee22c13ea7a9a67", name: "manage-node-3" }
```
### gather fact with ansible playbook
```
---
- name: Gather facts using the setup module
  hosts: all  # Replace with your target hosts 
  become: true
  tasks:
    - name: Gather all facts
      ansible.builtin.setup:

    - name: Display gathered facts
      ansible.builtin.debug:
        var: ansible_facts

```
- commands
- to create or to start the ec2
```
ansible-playbook ec2-create.yml --vault-password-file vault.pass
```
- for password less authentication (check for sg group allow ec2 in ami)
```
ssh-copy-id -f "-o IdentityFile ~/Downloads/deepu.pem" ubuntu@18.216.205.30
```
- for delete the ec2 with the help of family name
```
ansible-playbook -i inventory.ini ec2-stop.yaml --vault-password-file vault.pass
```

- to create the role for not hardcode
```
ansible-galaxy role init ec2

```
