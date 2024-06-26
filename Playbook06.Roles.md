### 16. Roles Playbook in Ansible

Roles in Ansible provide a way to group tasks, handlers, variables, files, templates, and modules in a structured manner. 
This modular approach allows for reusable and maintainable playbooks. 
This section covers structuring roles, role dependencies, using `include_role` and `import_role`, and role testing and validation with detailed examples and step-by-step instructions.

#### Structuring Roles

A typical role in Ansible has a specific directory structure. Each directory serves a different purpose and helps in organizing the components of the role.

**Example: Role Directory Structure**

```bash
my_role/
├── defaults
│   └── main.yml
├── files
│   └── my_file.txt
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
│   └── my_template.j2
├── tests
│   └── test.yml
└── vars
    └── main.yml
```

**Description of Each Directory:**

- **defaults:** Contains default variables for the role.
- **vars:** Contains variables that override defaults.
- **tasks:** Contains the main list of tasks to be executed.
- **handlers:** Contains handlers triggered by tasks.
- **files:** Contains static files to be copied to the target hosts.
- **templates:** Contains Jinja2 templates to be rendered and deployed.
- **meta:** Contains metadata about the role, such as dependencies.
- **tests:** Contains playbooks and tests to validate the role.

**Example: Main Task File (`tasks/main.yml`)**

```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present

- name: Copy NGINX config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart NGINX

- name: Ensure NGINX is running
  service:
    name: nginx
    state: started
    enabled: yes
```

**Example: Handler File (`handlers/main.yml`)**

```yaml
---
- name: Restart NGINX
  service:
    name: nginx
    state: restarted
```

**Example: Default Variables (`defaults/main.yml`)**

```yaml
---
nginx_port: 80
server_name: example.com
backend_host: 127.0.0.1
backend_port: 8080
```

**Example: Template File (`templates/nginx.conf.j2`)**

```jinja2
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};

    location / {
        proxy_pass http://{{ backend_host }}:{{ backend_port }};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

#### Role Dependencies

Role dependencies are defined in the `meta/main.yml` file. They allow you to specify other roles that must be applied before the current role.

**Example: Role Dependencies (`meta/main.yml`)**

```yaml
---
dependencies:
  - { role: common, vars: { some_var: 'value' } }
  - { role: security }
```

In this example:
- The `common` role is applied first with a specific variable.
- The `security` role is applied next.

#### Using `include_role` and `import_role`

Roles can be included or imported within playbooks using `include_role` and `import_role`. 
The difference is that `import_role` is static and happens at parse time, while `include_role` is dynamic and happens at runtime.

**Example: Using `include_role`**

```yaml
---
- hosts: webservers
  become: yes

  tasks:
    - name: Include common role
      include_role:
        name: common

    - name: Include webserver role
      include_role:
        name: webserver
```

**Example: Using `import_role`**

```yaml
---
- hosts: webservers
  become: yes

  tasks:
    - name: Import common role
      import_role:
        name: common

    - name: Import webserver role
      import_role:
        name: webserver
```

#### Role Testing and Validation

Testing and validation ensure that roles work as expected. The `tests` directory typically contains playbooks and test cases.

**Example: Test Playbook (`tests/test.yml`)**

```yaml
---
- name: Test NGINX role
  hosts: localhost
  become: yes
  roles:
    - my_role

  tasks:
    - name: Check if NGINX is installed
      command: nginx -v
      register: nginx_version
      failed_when: nginx_version.rc != 0

    - name: Check NGINX configuration
      command: nginx -t
      register: nginx_test
      failed_when: nginx_test.rc != 0
```

**Running the Test Playbook**

```bash
ansible-playbook tests/test.yml
```

#### Putting It All Together

**Example: Complete Role**

1. **Role Directory Structure:**

```bash
my_role/
├── defaults
│   └── main.yml
├── files
│   └── my_file.txt
├── handlers
│   └── main.yml
├── meta
│   └── main.yml
├── tasks
│   └── main.yml
├── templates
│   └── nginx.conf.j2
├── tests
│   └── test.yml
└── vars
    └── main.yml
```

2. **Main Task File (`tasks/main.yml`):**

```yaml
---
- name: Install NGINX
  apt:
    name: nginx
    state: present

- name: Copy NGINX config
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/nginx.conf
  notify: Restart NGINX

- name: Ensure NGINX is running
  service:
    name: nginx
    state: started
    enabled: yes
```

3. **Handler File (`handlers/main.yml`):**

```yaml
---
- name: Restart NGINX
  service:
    name: nginx
    state: restarted
```

4. **Default Variables (`defaults/main.yml`):**

```yaml
---
nginx_port: 80
server_name: example.com
backend_host: 127.0.0.1
backend_port: 8080
```

5. **Template File (`templates/nginx.conf.j2`):**

```jinja2
server {
    listen {{ nginx_port }};
    server_name {{ server_name }};

    location / {
        proxy_pass http://{{ backend_host }}:{{ backend_port }};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

6. **Role Dependencies (`meta/main.yml`):**

```yaml
---
dependencies:
  - { role: common, vars: { some_var: 'value' } }
  - { role: security }
```

7. **Test Playbook (`tests/test.yml`):**

```yaml
---
- name: Test NGINX role
  hosts: localhost
  become: yes
  roles:
    - my_role

  tasks:
    - name: Check if NGINX is installed
      command: nginx -v
      register: nginx_version
      failed_when: nginx_version.rc != 0

    - name: Check NGINX configuration
      command: nginx -t
      register: nginx_test
      failed_when: nginx_test.rc != 0
```

8. **Using the Role in a Playbook:**

```yaml
---
- hosts: webservers
  become: yes

  roles:
    - my_role
```

### Conclusion

Roles in Ansible provide a powerful way to organize and reuse your automation tasks. 
By structuring your roles correctly, defining dependencies, and using `include_role` and `import_role`, you can create modular and maintainable playbooks. 

Testing and validation ensure that your roles work as expected, leading to more reliable and predictable automation. 
With the detailed examples and explanations provided, you should be able to create and use roles effectively in your Ansible projects.
