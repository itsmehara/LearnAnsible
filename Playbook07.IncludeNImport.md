### 17. Include and Import

Ansible provides mechanisms to include and import tasks, playbooks, and roles within other playbooks. 
This modular approach helps in organizing and reusing code effectively. 
This section covers using `include_tasks` and `import_tasks`, the differences between `include` and `import`, 
and how to import playbooks and roles with detailed examples and step-by-step instructions.

#### Using `include_tasks` and `import_tasks`

**`include_tasks`** and **`import_tasks`** are used to include tasks from other files into your playbook.

- **`include_tasks`**: Dynamically includes tasks during the playbook execution.
- **`import_tasks`**: Statically includes tasks at the time the playbook is parsed.

**Example: Using `include_tasks`**

Create a file named `tasks/setup.yml`:

```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present

- name: Ensure NGINX is running
  service:
    name: nginx
    state: started
    enabled: yes
```

Create a playbook named `site.yml`:

```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Include setup tasks
      include_tasks: tasks/setup.yml
```

**Example: Using `import_tasks`**

Create a file named `tasks/deploy.yml`:

```yaml
---
- name: Copy application files
  copy:
    src: /app/src/
    dest: /var/www/html/

- name: Restart application service
  service:
    name: myapp
    state: restarted
```

Create a playbook named `deploy.yml`:

```yaml
---
- name: Deploy Application
  hosts: webservers
  become: yes

  tasks:
    - name: Import deploy tasks
      import_tasks: tasks/deploy.yml
```

#### Differences between `include` and `import`

**`include`** and **`import`** are used for including other playbooks and roles, and the main difference lies in how they handle the inclusion:

- **`include`**: Dynamic inclusion, tasks are included at runtime.
- **`import`**: Static inclusion, tasks are included at parse time.

**Example: Using `include_playbook`**

Create a file named `common.yml`:

```yaml
---
- name: Common setup tasks
  hosts: all
  become: yes

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
```

Create a playbook named `site.yml`:

```yaml
---
- name: Setup and Deploy
  hosts: webservers
  become: yes

  tasks:
    - name: Include common setup
      include_playbook: common.yml

    - name: Include webserver setup
      include_tasks: tasks/setup.yml

    - name: Include deploy tasks
      include_tasks: tasks/deploy.yml
```

**Example: Using `import_playbook`**

Create a file named `security.yml`:

```yaml
---
- name: Security setup tasks
  hosts: all
  become: yes

  tasks:
    - name: Install fail2ban
      apt:
        name: fail2ban
        state: present
```

Create a playbook named `main.yml`:

```yaml
---
- name: Complete setup
  hosts: all
  become: yes

  tasks:
    - name: Import security setup
      import_playbook: security.yml
```

#### Importing Playbooks and Roles

**Importing Playbooks**

Playbooks can be imported using `import_playbook`. This is useful for breaking down complex playbooks into smaller, manageable ones.

**Example: Importing Playbooks**

Create a playbook named `database.yml`:

```yaml
---
- name: Setup Database
  hosts: dbservers
  become: yes

  tasks:
    - name: Install PostgreSQL
      apt:
        name: postgresql
        state: present
```

Create a playbook named `webserver.yml`:

```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

Create a playbook named `main.yml`:

```yaml
---
- name: Complete Infrastructure Setup
  hosts: all
  become: yes

  tasks:
    - name: Import database setup
      import_playbook: database.yml

    - name: Import webserver setup
      import_playbook: webserver.yml
```

**Importing Roles**

Roles can be imported using `import_role`. This is useful for including roles with predefined tasks, handlers, and variables.

**Example: Using `import_role`**

Create a role directory named `roles/nginx`:

```bash
roles/nginx/
├── defaults
│   └── main.yml
├── tasks
│   └── main.yml
```

**`roles/nginx/defaults/main.yml`:**

```yaml
---
nginx_port: 80
```

**`roles/nginx/tasks/main.yml`:**

```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present

- name: Ensure NGINX is running
  service:
    name: nginx
    state: started
    enabled: yes
```

Create a playbook named `setup.yml`:

```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Import NGINX role
      import_role:
        name: nginx
```

#### Putting It All Together

**Example: Comprehensive Playbook with Include and Import**

Create the following directory structure:

```bash
.
├── tasks
│   ├── setup.yml
│   ├── deploy.yml
├── roles
│   └── nginx
│       ├── defaults
│       │   └── main.yml
│       └── tasks
│           └── main.yml
├── common.yml
├── security.yml
├── database.yml
├── webserver.yml
└── main.yml
```

**`tasks/setup.yml`:**

```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present

- name: Ensure NGINX is running
  service:
    name: nginx
    state: started
    enabled: yes
```

**`tasks/deploy.yml`:**

```yaml
---
- name: Copy application files
  copy:
    src: /app/src/
    dest: /var/www/html/

- name: Restart application service
  service:
    name: myapp
    state: restarted
```

**`roles/nginx/defaults/main.yml`:**

```yaml
---
nginx_port: 80
```

**`roles/nginx/tasks/main.yml`:**

```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present

- name: Ensure NGINX is running
  service:
    name: nginx
    state: started
    enabled: yes
```

**`common.yml`:**

```yaml
---
- name: Common setup tasks
  hosts: all
  become: yes

  tasks:
    - name: Update apt cache
      apt:
        update_cache: yes
```

**`security.yml`:**

```yaml
---
- name: Security setup tasks
  hosts: all
  become: yes

  tasks:
    - name: Install fail2ban
      apt:
        name: fail2ban
        state: present
```

**`database.yml`:**

```yaml
---
- name: Setup Database
  hosts: dbservers
  become: yes

  tasks:
    - name: Install PostgreSQL
      apt:
        name: postgresql
        state: present
```

**`webserver.yml`:**

```yaml
---
- name: Setup Web Server
  hosts: webservers
  become: yes

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present
```

**`main.yml`:**

```yaml
---
- name: Complete Infrastructure Setup
  hosts: all
  become: yes

  tasks:
    - name: Import common setup
      import_playbook: common.yml

    - name: Import security setup
      import_playbook: security.yml

    - name: Import database setup
      import_playbook: database.yml

    - name: Import webserver setup
      import_playbook: webserver.yml

    - name: Include setup tasks
      include_tasks: tasks/setup.yml

    - name: Include deploy tasks
      include_tasks: tasks/deploy.yml

    - name: Import NGINX role
      import_role:
        name: nginx
```

### Conclusion

Understanding how to use `include_tasks` and `import_tasks`, as well as the differences between `include` and `import`, 
is crucial for creating modular and maintainable Ansible playbooks. 
By breaking down complex playbooks into smaller, reusable components, you can manage your infrastructure more effectively. 
The provided examples and detailed explanations should help you grasp these concepts and apply them in your Ansible projects.


