# ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1

## Introducing dynamic assignment into the structure.

 These are folders which are introduced which are have enviroment specific variables

  First we create a folder called dynamic assigment and innthe folder we create a file called 

env-vars.yml which will be included later using a command that will be passed to site.yml file.

![image](https://github.com/NANA-2016/ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1/assets/141503408/f86c3ccb-054c-4b92-aeb1-62abdc2ef9b9)

We also need to create a folder by the name env-vars and then create a new yml for each enviroment 

which include dev.yml,stage.yml,uat.yml and prod.yml. These will help us configure multiple 

enviroments with different unique attributes

![image](https://github.com/NANA-2016/ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1/assets/141503408/0a3fb863-b81a-4905-8689-bd66176ea2f3)

Using the code below, which allows differentiation of different features of the module  by 

##### using include_vars instead of include  in the env-vars.yml file.

---
- name: collate variables from env specific file, if it exists
  hosts: all
  tasks:
    - name: looping through list of available files
      include_vars: "{{ item }}"
      with_first_found:
        - files:
            - dev.yml
            - stage.yml
            - prod.yml
            - uat.yml
          paths:
            - "{{ playbook_dir }}/../env-vars"
      tags:
        - always

##### Next step is to update site.yml file in the playbook folder  withthe command below to allow
 
 use of dynamic assigments

 ---
- hosts: all
- name: Include dynamic variables 
  tasks:
  import_playbook: ../static-assignments/common.yml 
  include: ../dynamic-assignments/env-vars.yml
  tags:
    - always

-  hosts: webservers
- name: Webserver assignment
  import_playbook: ../static-assignments/webservers.yml

![image](https://github.com/NANA-2016/ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1/assets/141503408/6d40b500-7c87-4e2e-b6a8-614b772cc2ee)

# update site.yml with dynamic assigments

## use of community roles.

 We will use geerlingguy comminty roles to install mysql,apache or 
 
 nginx as load balancerand rename them.

  The commands are below.

sudo ansible-galaxy install geerlingguy.mysql

sudo mv geerlingguy.mysql/ msql

  sudo ansible-galaxy install geerlingguy.apache

sudo mv geerlingguy.apache/ apache-lb

  sudo ansible-galaxy install geerlingguy.nginx

sudo mv geerlingguy.nginx/ nginx.lb

All these will be found of the roles folder 

![image](https://github.com/NANA-2016/ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1/assets/141503408/d8b79425-60df-4fc4-942e-095d7aff50c3)

 #####  Declare a variable in default/main.yml inside nginx and apache roles folders and name each variable to enable_nginx_lb and enable_apache _lb as well as the
   Load_balancer_is _required:false

   seen ats the last two lines on the page
   
![image](https://github.com/NANA-2016/ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1/assets/141503408/3a57699b-f58b-4048-b47b-c610eebe5775)


##### Also you need to update site.yml with the code below

     - name: Loadbalancers assignment
       hosts: lb
         - import_playbook: ../static-assignments/loadbalancers.yml
        when: load_balancer_is_required 

#### Update the load balancer 

- hosts: lb
  roles:
    - { role: nginx, when: enable_nginx_lb and load_balancer_is_required }
    - { role: apache, when: enable_apache_lb and load_balancer_is_required }
      
update the the env-vars file with the command below to activate the load balancer

enable_nginx_lb: true
load_balancer_is_required: true

 You have to ensure nginx has been uploaded in both servers as it is the load balancer we decided to use in this project.

![image](https://github.com/NANA-2016/ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1/assets/141503408/1c2a1f12-67ce-498f-902a-fd5fdff6ff32)

![image](https://github.com/NANA-2016/ANSIBLE-DYNAMIC-ASSIGNMENTS-INCLUDE-AND-COMMUNITY-ROLES-1/assets/141503408/73fbf953-8926-4099-acb0-2fd55c2a5d88)


###### I could not use the VS CODE in all my implementation as my LAPTOP SSH is not working.



