https://galaxy.ansible.com/ui/standalone/roles/  
this is the web for role
  i used to install docker (roles inside it)
     https://galaxy.ansible.com/ui/standalone/roles/walidsa3d/ansidocker/
day  6
create vault for authenitcain we can use as local user here we directly talk with aws with help of authentication help of IAM user

 after creating the playbook.yaml
 ---
# tasks file for ec2
- name: start an instance with a pu
  amazon.aws.ec2_instance:
    name: "ansible-instance"
    # key_name: "prod-ssh-key"
    # vpc_subnet_id: subnet-013744e
    instance_type: t2.micro
    security_group: default
    region: us-east-1
    aws_access_key: "{{ec2_access_k
    aws_secret_key: "{{ec2_secret_k
    network:
      assign_public_ip: true
    image_id: ami-04b70fa74e45c3917


  this is the playbook.yaml here we the ansible collection

  host will be provided

  after that in the same location
  we have to create a vault

  Create a password for vault

  openssl rand -base64 2048 > vault.pass

  after this( Add your AWS credentials using the below vault command  )
  
  ansible-vault create group_vars/all/pass.yml --vault-password-file vault.pass

  to run the ansible

  ansible-playbook -i <host name> <playbook name> --vault-password-file vault.pass

  be in the proper location and check the roles and inside the  playbook correct location are  provided or not 

  
