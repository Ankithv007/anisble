<h3> error handling in the ansible playbook </h3>
<h4> there are different types present in error handling</h4>
<h5> add the host to host file else connect through api help of ami aws configure </h5>
<h5> it's helps in whether the docker is installed and open ssh and ssl are present in the ec2</h5>

---
- hosts: all
  become: true

  tasks:
    - name: Install security updates
      ansible.builtin.apt:
        name: "{{ item }}"
        state: latest
      loop:
        - openssl
        - openssh
      ignore_errors: yes 
    - name: Check if docker is installed
      ansible.builtin.command: docker --version
      register: output
      ignore_errors: yes    
    - ansible.builtin.debug:
        var: output
    - name: Install docker
      ansible.builtin.apt:
        name: docker.io
        state: present
      when: output.failed
