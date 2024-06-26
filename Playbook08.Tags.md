### 18. Tags in Playbook Ansible

Tags in Ansible allow you to control which parts of your playbooks are executed. 
This is useful for testing, debugging, or running specific subsets of tasks without executing the entire playbook. 
Here we will cover assigning tags to tasks and plays, running playbooks with specific tags, and skipping tasks with tags, along with detailed examples and step-by-step instructions.

#### Assigning Tags to Tasks and Plays

Tags can be assigned to tasks and plays in your playbooks. When you run a playbook, you can specify which tags to include or exclude.

**Example: Assigning Tags to Tasks**

Create a playbook named `site.yml`:

```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
      tags:
        - web
        - nginx

    - name: Install MySQL
      apt:
        name: mysql-server
        state: present
      tags:
        - db
        - mysql

    - name: Start NGINX
      service:
        name: nginx
        state: started
      tags:
        - web
        - nginx

    - name: Start MySQL
      service:
        name: mysql
        state: started
      tags:
        - db
        - mysql
```

**Example: Assigning Tags to Plays**

Create a playbook named `multi_play.yml`:

```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes
  tags: web
  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
      tags:
        - nginx

    - name: Start NGINX
      service:
        name: nginx
        state: started
      tags:
        - nginx

- name: Setup Database Server
  hosts: dbservers
  become: yes
  tags: db
  tasks:
    - name: Install MySQL
      apt:
        name: mysql-server
        state: present
      tags:
        - mysql

    - name: Start MySQL
      service:
        name: mysql
        state: started
      tags:
        - mysql
```

#### Running Playbooks with Specific Tags

When running a playbook, you can use the `--tags` option to specify which tags to include or the `--skip-tags` option to specify which tags to exclude.

**Example: Running Playbooks with Specific Tags**

Run the playbook to include only tasks with the `web` tag:

```bash
ansible-playbook site.yml --tags "web"
```

Run the playbook to include only tasks with the `nginx` tag:

```bash
ansible-playbook site.yml --tags "nginx"
```

Run the playbook to exclude tasks with the `mysql` tag:

```bash
ansible-playbook site.yml --skip-tags "mysql"
```

**Example: Running Plays with Specific Tags**

Run the multi-playbook to include only the `web` play:

```bash
ansible-playbook multi_play.yml --tags "web"
```

Run the multi-playbook to include only the `db` play:

```bash
ansible-playbook multi_play.yml --tags "db"
```

#### Skipping Tasks with Tags

The `--skip-tags` option allows you to skip tasks with specific tags when running a playbook.

**Example: Skipping Tasks with Specific Tags**

Create a playbook named `site_with_skip.yml`:

```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
      tags:
        - web
        - nginx

    - name: Install MySQL
      apt:
        name: mysql-server
        state: present
      tags:
        - db
        - mysql

    - name: Start NGINX
      service:
        name: nginx
        state: started
      tags:
        - web
        - nginx

    - name: Start MySQL
      service:
        name: mysql
        state: started
      tags:
        - db
        - mysql
```

Run the playbook to skip tasks with the `nginx` tag:

```bash
ansible-playbook site_with_skip.yml --skip-tags "nginx"
```

#### Combining Tags and Skipping Tags

You can combine the use of `--tags` and `--skip-tags` to fine-tune which tasks are executed.

**Example: Combining Tags and Skipping Tags**

Create a playbook named `combined_tags.yml`:

```yaml
---
- name: Setup Web Server and Database
  hosts: all
  become: yes

  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present
      tags:
        - web
        - nginx

    - name: Install MySQL
      apt:
        name: mysql-server
        state: present
      tags:
        - db
        - mysql

    - name: Start NGINX
      service:
        name: nginx
        state: started
      tags:
        - web
        - nginx

    - name: Start MySQL
      service:
        name: mysql
        state: started
      tags:
        - db
        - mysql
```

Run the playbook to include only `web` tasks but skip `nginx` tasks:

```bash
ansible-playbook combined_tags.yml --tags "web" --skip-tags "nginx"
```

### Conclusion

Tags in Ansible provide a powerful mechanism to selectively run parts of your playbooks. 
By assigning tags to tasks and plays, you can control which operations are executed, making your automation workflows more flexible and efficient. 

The provided examples and detailed explanations should help you understand and utilize tags effectively in your Ansible projects.
