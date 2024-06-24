### 2. Variables and Facts in Ansible

Understanding variables and facts is crucial for effective use of Ansible. 
Variables allow you to customize and control your playbooks, while facts provide detailed information about your managed nodes that you can use within your playbooks. 
Here’s an exhaustive guide on these topics:

#### Defining Variables

Variables in Ansible are used to store values that can be referenced throughout your playbooks. 
They can be defined in several places, each with its own scope and precedence.

**Ways to Define Variables:**

1. **In Playbooks:**
   - You can define variables directly within a playbook:
     ```yaml
     ---
     - name: Example Playbook
       hosts: webservers
       vars:
         http_port: 80
         max_clients: 200
       tasks:
         - name: Ensure Nginx is installed
           apt:
             name: nginx
             state: present
         - name: Configure Nginx
           template:
             src: nginx.conf.j2
             dest: /etc/nginx/nginx.conf
     ```

2. **In Inventory Files:**
   - Variables can be defined in inventory files for hosts or groups:
     ```ini
     [webservers]
     webserver1.example.com http_port=80 max_clients=200
     webserver2.example.com http_port=8080 max_clients=100

     [dbservers]
     dbserver1.example.com
     dbserver2.example.com
     ```

3. **In Variable Files:**
   - Variables can be stored in separate YAML files and included in playbooks:
     ```yaml
     # vars.yml
     http_port: 80
     max_clients: 200
     ```
     ```yaml
     ---
     - name: Example Playbook
       hosts: webservers
       vars_files:
         - vars.yml
       tasks:
         - name: Ensure Nginx is installed
           apt:
             name: nginx
             state: present
     ```

4. **In Roles:**
   - Variables can be defined within roles in `defaults` and `vars` directories:
     ```yaml
     # roles/myrole/defaults/main.yml
     http_port: 80
     max_clients: 200
     ```

5. **As Extra Variables:**
   - Extra variables can be passed on the command line using `-e`:
     ```bash
     ansible-playbook example.yml -e "http_port=8080 max_clients=300"
     ```

#### Variable Precedence and Scopes

Ansible variables have different levels of precedence depending on where they are defined. Here is the order from lowest to highest precedence:

1. **Role defaults (defined in `defaults/main.yml`).**
2. **Inventory file or script group vars.**
3. **Inventory group_vars/all.**
4. **Playbook group_vars/all.**
5. **Inventory group_vars/* (each file in lexicographical order).**
6. **Playbook group_vars/* (each file in lexicographical order).**
7. **Inventory file or script host vars.**
8. **Inventory host_vars/* (each file in lexicographical order).**
9. **Playbook host_vars/* (each file in lexicographical order).**
10. **Host facts / cached set_facts.**
11. **Play vars_files.**
12. **Play vars_prompt.**
13. **Play vars (defined in `vars`).**
14. **Playbook or role included vars (by include_vars).**
15. **Set_facts / registered variables.**
16. **Role vars (defined in `vars/main.yml`).**
17. **Block vars (only for tasks in block).**
18. **Task vars (only for the task).**
19. **Extra vars (defined on command line with `-e`).**

This precedence determines which variable value is used when there are multiple definitions.

#### Using Host, Group, and Playbook Variables

1. **Host Variables:**
   - Specific to individual hosts, defined in the inventory or host_vars files:
     ```ini
     [webservers]
     webserver1.example.com ansible_user=ubuntu
     ```

2. **Group Variables:**
   - Applied to all hosts in a group, defined in the inventory or group_vars files:
     ```ini
     [webservers]
     webserver1.example.com
     webserver2.example.com

     [webservers:vars]
     http_port=80
     ```

3. **Playbook Variables:**
   - Defined within the playbook under `vars` or `vars_files`:
     ```yaml
     ---
     - name: Example Playbook
       hosts: webservers
       vars:
         http_port: 80
         max_clients: 200
       tasks:
         - name: Ensure Nginx is installed
           apt:
             name: nginx
             state: present
     ```

#### Registering and Using Facts

Facts are variables that Ansible automatically discovers about the managed nodes. You can also create your own facts using the `set_fact` module.

**Using Built-In Facts:**
- Facts are gathered by default at the beginning of each playbook run.
- You can access facts using the `ansible_facts` prefix:
  ```yaml
  - name: Print OS family
    debug:
      msg: "The OS family is {{ ansible_facts['os_family'] }}"
  ```

**Registering Facts:**
- You can register the output of a task as a fact to use later in the playbook:
  ```yaml
  - name: Check the uptime
    command: uptime
    register: uptime_result

  - name: Print uptime result
    debug:
      msg: "The system uptime is {{ uptime_result.stdout }}"
  ```

**Custom Facts:**
- Create custom facts using the `set_fact` module:
  ```yaml
  - name: Set custom fact
    set_fact:
      my_custom_fact: "This is a custom fact"

  - name: Print custom fact
    debug:
      msg: "{{ my_custom_fact }}"
  ```

#### Default Variables and Overriding Variables

Default variables provide baseline values that can be overridden by higher-precedence variables.

**Using Defaults in Roles:**
- Define default variables in `defaults/main.yml` within a role:
  ```yaml
  # roles/myrole/defaults/main.yml
  http_port: 80
  max_clients: 200
  ```

**Overriding Defaults:**
- Override default variables by defining higher-precedence variables in the inventory, playbook, or extra variables:
  ```yaml
  # playbook.yml
  - name: Example Playbook
    hosts: webservers
    vars:
      http_port: 8080
    roles:
      - myrole
  ```

**Example of Variable Override Precedence:**
```yaml
---
- name: Demonstrate variable precedence
  hosts: all
  vars:
    common_var: "playbook var"
  tasks:
    - name: Print variable
      debug:
        msg: "common_var is {{ common_var }}"
```

Running this playbook with different variable definitions will show which variable takes precedence. For example:
```bash
ansible-playbook example.yml -e "common_var='extra var'"
```

This will print:
```
common_var is extra var
```

By mastering these aspects of variables and facts in Ansible, you'll be able to write more flexible, powerful, and maintainable automation scripts. 
This knowledge is essential for customizing playbooks to different environments and managing complex infrastructure efficiently.
