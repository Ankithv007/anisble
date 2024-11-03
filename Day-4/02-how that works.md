- create role with ansibel-galaxy
``` ansible-galaxy role init <any name you want>```
- once you hit this cmd one folder will be created inside diffrent folder and files(main.yml) will be created

last day your only having like 
- first-playbook.yaml
- index.html
- inventory.in

### first-playbook.yaml (inside playbook.yaml you have to mention creted role 
```
---
- hosts: all
  become: true
  roles:
    - index  // this is the role you have created of it is in different name just change it

```
### so your index.html goes inside files folder in role folder 
- add you index file inside there
- add your task inside tasks folder and also change the your index files in role files alwasy inside the files folder
  
- her your gone have 4 file after you hit that command one more file will be added 
- so you have to push to the index.html to files folder files  folder
-  `mv index.html <your folder name>/files`
  
- After this modify your  first-playbook.yaml add the task and task name inside
- copy and paste in the folder task and modify if neccasery in this case you have add the path of index file in task folder inside main.yml

  - after all the run your ansible playbook.yml
  ` ansible-playbook -i inventory.ini first-playbook.yaml `
-if get a bug goto chat gpt 😂
