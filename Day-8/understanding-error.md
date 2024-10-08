### here we going on after next to understand how the error handles works
- without error handles we are gonna chack open ssl and ssh are present
```
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
```
- with error handles
```
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
```
- here its ignore error in task 1 and it goes for task2 you know why becaues we use ignore_error: yes   in default ansible works
- in defautly anible does not provide above point if task1 fails it does go for task 2 on that specific machine  
```
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
```
- here we use in variable input and output to if the last task fails with the help of that last task we got to know the presence,so we can use in next task
```
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

    - name: Install docker
      ansible.builtin.apt:
        name: docker.io
        state: present
      when: output.failed
```
- lastly if not works
```
---
- hosts: all
  become: true

  tasks:
    - name: Update the apt package index
      ansible.builtin.apt:
        update_cache: yes

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



      
