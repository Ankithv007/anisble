- create role with ansibel-galaxy
``` ansible-galaxy role init <any name you want>```
- once you hit this cmd one folder will be created inside diffrent folder and files(main.yml) will be created

last day your only having like 
- first-playbook.yaml
- index.html
- inventory.in

- her your gone have 4 file after you hit that command one more file will be added 
- so you have to push to the index.html to files folder files  folder
-  `mv index.html <your folder name>/files`
  
- After this modify your  first-playbook.yaml add the task and task name inside
- copy and paste in the folder task and modify if neccasery in this case you have add the path of index file in task folder inside main.yml

  - after all the run your ansible playbook.yml
  ` ansible-playbook -i inventory.ini first-playbook.yaml `
-if get a bug goto chat gpt 😂
