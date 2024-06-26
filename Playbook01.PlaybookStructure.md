
### 11. Playbook Structure

Understanding the structure of Ansible playbooks is fundamental to effectively managing configurations and automation tasks. 
This section covers the key components of a playbook, including the playbook header, plays, tasks, and modules. 

Detailed step-by-step instructions and examples are provided to help you understand these concepts clearly.

#### Playbook Header

The playbook header includes metadata about the playbook, such as its name, the hosts it will be run against, and connection details.

**Example: Basic Playbook Header**

```yaml
---
- name: Ensure web servers are configured
  hosts: webservers
  become: yes
  gather_facts: yes
  vars:
    apache_package: apache2
```

**Key Components:**
- **name:** A descriptive name for the playbook.
- **hosts:** The group of hosts or specific hosts the playbook will run against.
- **become:** Whether to escalate privileges (e.g., run as root).
- **gather_facts:** Whether to gather system information (facts) about the hosts.
- **vars:** Variables that can be used within the playbook.

#### Plays

A playbook consists of one or more plays. Each play defines a set of tasks to be executed on a group of hosts.

**Example: Play with Multiple Tasks**

```yaml
---
- name: Ensure web servers are configured
  hosts: webservers
  become: yes
  gather_facts: yes

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Start Apache service
      service:
        name: apache2
        state: started
```

**Key Components:**
- **tasks:** A list of tasks to be executed on the specified hosts.

#### Tasks

Tasks are individual units of work within a play. Each task performs a specific action using a module.

**Example: Tasks in a Play**

```yaml
---
- name: Ensure web servers are configured
  hosts: webservers
  become: yes
  gather_facts: yes

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Copy Apache config
      template:
        src: apache2.conf.j2
        dest: /etc/apache2/apache2.conf
        mode: 0644

    - name: Ensure Apache is running
      service:
        name: apache2
        state: started
        enabled: yes
```

**Key Components:**
- **name:** A descriptive name for the task.
- **module:** The Ansible module that performs the action (e.g., `apt`, `template`, `service`).

#### Modules

Modules are units of work within tasks that perform specific actions. Ansible provides a wide variety of modules for different tasks, such as package management, file manipulation, and service management.

**Example: Commonly Used Modules**

1. **apt:** Manages packages on Debian-based systems.
   ```yaml
   - name: Install Apache
     apt:
       name: apache2
       state: present
   ```

2. **template:** Manages file content using Jinja2 templates.
   ```yaml
   - name: Copy Apache config
     template:
       src: apache2.conf.j2
       dest: /etc/apache2/apache2.conf
       mode: 0644
   ```

3. **service:** Manages services.
   ```yaml
   - name: Ensure Apache is running
     service:
       name: apache2
       state: started
       enabled: yes
   ```

#### Example Playbook

Let's put everything together in a complete example playbook that installs and configures Apache on web servers.

**Example: Complete Playbook**

```yaml
---
- name: Ensure web servers are configured
  hosts: webservers
  become: yes
  gather_facts: yes

  vars:
    apache_package: apache2
    apache_config_src: apache2.conf.j2
    apache_config_dest: /etc/apache2/apache2.conf

  tasks:
    - name: Install Apache
      apt:
        name: "{{ apache_package }}"
        state: present

    - name: Copy Apache config
      template:
        src: "{{ apache_config_src }}"
        dest: "{{ apache_config_dest }}"
        mode: 0644

    - name: Ensure Apache is running
      service:
        name: apache2
        state: started
        enabled: yes
```

**Explanation:**
- **Playbook Header:** Defines the name, hosts, privilege escalation, and fact-gathering settings.
- **Variables:** Defines variables for package name and configuration file paths.
- **Tasks:**
  - **Install Apache:** Installs the Apache package using the `apt` module.
  - **Copy Apache Config:** Copies the Apache configuration file using the `template` module.
  - **Ensure Apache is Running:** Ensures the Apache service is started and enabled using the `service` module.

### Additional Examples and Tips

#### Using Variables in Tasks

Variables can be defined in the playbook header or in separate files and used in tasks to make playbooks more flexible and reusable.

**Example: Using Variables**

```yaml
---
- name: Ensure web servers are configured
  hosts: webservers
  become: yes
  gather_facts: yes

  vars:
    packages:
      - apache2
      - php
      - mysql-server

  tasks:
    - name: Install necessary packages
      apt:
        name: "{{ item }}"
        state: present
      loop: "{{ packages }}"
```

#### Conditional Tasks

Tasks can be executed conditionally using the `when` keyword.

**Example: Conditional Task Execution**

```yaml
---
- name: Ensure web servers are configured
  hosts: webservers
  become: yes
  gather_facts: yes

  tasks:
    - name: Install Apache on Debian-based systems
      apt:
        name: apache2
        state: present
      when: ansible_os_family == "Debian"

    - name: Install Apache on RedHat-based systems
      yum:
        name: httpd
        state: present
      when: ansible_os_family == "RedHat"
```

#### Handlers

Handlers are tasks that are triggered by other tasks. They are typically used for actions that should only happen if a change occurs, such as restarting a service after a configuration file is updated.

**Example: Using Handlers**

```yaml
---
- name: Ensure web servers are configured
  hosts: webservers
  become: yes
  gather_facts: yes

  tasks:
    - name: Install Apache
      apt:
        name: apache2
        state: present

    - name: Copy Apache config
      template:
        src: apache2.conf.j2
        dest: /etc/apache2/apache2.conf
        mode: 0644
      notify: Restart Apache

  handlers:
    - name: Restart Apache
      service:
        name: apache2
        state: restarted
```

By following these guidelines and using the provided examples, you can create well-structured, efficient, and maintainable Ansible playbooks.
