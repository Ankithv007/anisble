# Ansible Realtime project

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

<h5> this is script is for create the instance </h5>
<h5> change the ami id and vault pass as per we provide /h5>

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
      - { image: "ami-0e1d06225679bc1c5", name: "manage-node-1" } # Update AMI ID according 
      - { image: "ami-0f58b397bc5c1f2e8", name: "manage-node-2" } # to your account
      - { image: "ami-0f58b397bc5c1f2e8", name: "manage-node-3" }
```
  <h4> to delete (shutdown) the instance by using there  ubuntu_family </h4>

```
  ---
- hosts: all
  become: true

  tasks:
    - name: Shutdown ubuntu instances only
      ansible.builtin.command: /sbin/shutdown -t now
      when:
       ansible_facts['os_family'] == "Debian"

```
<h4> by proving there ami id shudowning the insatces</h4>

```
---
- name: Delete AWS EC2 Instances by AMI ID
  hosts: localhost
  gather_facts: false
  vars:
    region: "ap-south-1"          # Replace with your desired AWS region
    ami_id: "ami-0ad21ae1d0696ad58"  # Replace with your AMI ID

  tasks:
    - name: Find instances with specific AMI ID
      amazon.aws.ec2_instance_facts:
        region: "{{ region }}"
        filters:
          image-id: "{{ ami_id }}"
      register: instances

    - name: Terminate instances with specific AMI ID
      amazon.aws.ec2_instance:
        state: absent
        instance_id: "{{ item.instance_id }}"
        region: "{{ region }}"
      loop: "{{ instances.instances }}"
      when: instances.instances | length > 0
      register: termination_results

    - name: Show the result of termination
      debug:
        var: termination_results

```      
