### 15. Templates Playbook

Templates in Ansible allow you to create dynamic files using the Jinja2 templating engine. 
This section covers creating Jinja2 templates, using variables in templates, and utilizing the template module to deploy these templates. 
Detailed step-by-step instructions and examples are provided to help you understand these concepts clearly.

#### Creating Jinja2 Templates

Jinja2 is a powerful templating engine used by Ansible to generate configuration files and other text-based files dynamically. 
Templates are written in Jinja2 syntax and saved with a `.j2` extension.

**Example: Basic Jinja2 Template**

Create a file named `nginx.conf.j2`:

```jinja2
server {
    listen 80;
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

In this template:
- `{{ server_name }}`, `{{ backend_host }}`, and `{{ backend_port }}` are variables that will be replaced with actual values when the template is rendered.

#### Using Variables in Templates

Variables in Jinja2 templates can be defined in the playbook or in an external variables file. They are passed to the template at runtime.

**Example: Defining Variables in a Playbook**

Create a playbook named `configure_nginx.yml`:

```yaml
---
- name: Configure NGINX
  hosts: webservers
  become: yes

  vars:
    server_name: example.com
    backend_host: 127.0.0.1
    backend_port: 8080

  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present

    - name: Deploy NGINX configuration
      template:
        src: nginx.conf.j2
        dest: /etc/nginx/sites-available/default
        mode: '0644'

    - name: Ensure NGINX is running
      service:
        name: nginx
        state: started
        enabled: yes
```

In this playbook:
- Variables `server_name`, `backend_host`, and `backend_port` are defined under the `vars` section.
- The `template` module is used to deploy the `nginx.conf.j2` template.

#### Template Module

The `template` module in Ansible is used to process Jinja2 templates and deploy the rendered files to the target hosts.

**Example: Using the Template Module**

```yaml
- name: Deploy NGINX configuration
  template:
    src: nginx.conf.j2
    dest: /etc/nginx/sites-available/default
    mode: '0644'
```

In this task:
- `src` specifies the source template file (`nginx.conf.j2`).
- `dest` specifies the destination path on the target host (`/etc/nginx/sites-available/default`).
- `mode` sets the file permissions.

#### Advanced Jinja2 Features

Jinja2 templates support advanced features such as conditionals, loops, and filters.

**Example: Using Conditionals in Jinja2 Templates**

```jinja2
server {
    listen 80;
    server_name {{ server_name }};

    {% if ssl_enabled %}
    listen 443 ssl;
    ssl_certificate /etc/nginx/ssl/{{ server_name }}.crt;
    ssl_certificate_key /etc/nginx/ssl/{{ server_name }}.key;
    {% endif %}

    location / {
        proxy_pass http://{{ backend_host }}:{{ backend_port }};
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

In this template:
- The `if` statement checks if `ssl_enabled` is true and includes the SSL configuration if it is.

**Example: Using Loops in Jinja2 Templates**

```jinja2
upstream backend {
    {% for server in backend_servers %}
    server {{ server.host }}:{{ server.port }};
    {% endfor %}
}

server {
    listen 80;
    server_name {{ server_name }};

    location / {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

In this template:
- The `for` loop iterates over the `backend_servers` list and generates server entries for each backend server.

**Example: Defining Variables in the Playbook for Advanced Templates**

```yaml
---
- name: Configure NGINX with advanced features
  hosts: webservers
  become: yes

  vars:
    server_name: example.com
    ssl_enabled: true
    backend_servers:
      - { host: '127.0.0.1', port: 8080 }
      - { host: '127.0.0.2', port: 8081 }

  tasks:
    - name: Install NGINX
      apt:
        name: nginx
        state: present

    - name: Deploy NGINX configuration with advanced features
      template:
        src: advanced_nginx.conf.j2
        dest: /etc/nginx/sites-available/default
        mode: '0644'

    - name: Ensure NGINX is running
      service:
        name: nginx
        state: started
        enabled: yes
```

In this playbook:
- `ssl_enabled` and `backend_servers` variables are defined.
- The `template` module is used to deploy the `advanced_nginx.conf.j2` template.

### Conclusion

Jinja2 templates in Ansible provide a powerful way to create dynamic configuration files and other text-based files. 
By using variables, conditionals, and loops, you can create flexible and reusable templates. 

The `template` module processes these templates and deploys the rendered files to the target hosts. 
With the provided examples and detailed explanations, you can effectively create and use Jinja2 templates in your Ansible playbooks.
