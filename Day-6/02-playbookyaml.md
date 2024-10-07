### playbook.yaml
```
--- 
- hosts: localhost
  connection: local
  tasks:
  - name: start an instance with a public IP address
    amazon.aws.ec2_instance:
      name: "ansible-instance"
      # key_name: "prod-ssh-key"
      # vpc_subnet_id: subnet-013744e41e8088axx
      instance_type: t2.micro
      security_group: default
      region: us-east-1
      aws_access_key: "{{ec2_access_key}}"  # From vault as defined
      aws_secret_key: "{{ec2_secret_key}}"  # From vault as defined      
      network:
        assign_public_ip: true
      image_id: ami-04b70fa74e45c3917
      tags:
        Environment: Testing
```
- to run
```
ansible-playbook -i inventory.ini ec2.yaml --vault-password-file vault.pass
```
###  in the ansible we can define variable in different place in different  folder there like 22 place to place the variable (assign in the form of jinja2 template)
- to look 22 place to assign the variable
<a href="https://docs.ansible.com/ansible/latest/playbook_guide/playbooks_variables.html#creating-valid-variable-names">Click here to look</a>

1. deafult (less proficiency)
2. variable (highest proficiency)
3. extra vars (higher than vars (extra vars "-e"))
4. vars we can write it in playbook it self on top the each and every play
### playbook.yaml ( for variable )
```
---
# tasks file for ec2
- name: start an instance with a public IP address
  amazon.aws.ec2_instance:
    name: "ansible-instance"
    # key_name: "prod-ssh-key"
    # vpc_subnet_id: subnet-013744e41e8088axx
    instance_type: "{{ type }}"
    security_group: default
    region: us-east-2
    aws_access_key: "{{ec2_access_key}}" # From vault as defined
    aws_secret_key: "{{ec2_secret_key}}" # From vault as defined
    network:
      assign_public_ip: true
    image_id: ami-0ea3c35c5c3284d82
    tags:
      Environment: Testing

```
-in the default.variable (inside main.yml)
```
---
# defaults fil
type: t2.micro
```
-to run
```
ansible-playbook -i inventory.ini ec2.yaml --vault-password-file vault.pass
```
-in the vars.variable (inside main.yml) ------> its has higest prefer so it create a t2.medium rather than t2.micro
```
---
# defaults fil
type: t2.medium
```
-to run
```
ansible-playbook -i inventory.ini ec2.yaml --vault-password-file vault.pass
```
### highest prefer than vars is extra vars we use directly in CLI command 
```
ansible-playbook -i inventory.ini ec2.yaml --vault-password-file vault.pass -e type=t2.micro
```

