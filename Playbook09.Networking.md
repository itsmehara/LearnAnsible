### 19. Networking with Ansible Playbook

Ansible is widely used for network automation, providing a simple, agentless approach to managing network devices and services. 
This section covers managing network devices and services, using network modules, and network automation and orchestration.

#### Managing Network Devices and Services

Ansible can manage a wide variety of network devices from different vendors, including Cisco, Juniper, Arista, and more. 
It can configure interfaces, manage VLANs, deploy network services, and perform many other tasks.

**Example: Managing Network Devices**

Create a playbook named `network_config.yml`:

```yaml
---
- name: Configure network devices
  hosts: switches
  gather_facts: no
  tasks:
    - name: Configure interface on Cisco switch
      cisco.ios.ios_interface:
        name: GigabitEthernet1
        description: Uplink to Router
        enabled: yes
        state: present
      vars:
        ansible_network_os: ios
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: network_cli

    - name: Configure VLAN on Juniper switch
      junipernetworks.junos.junos_vlan:
        vlan_id: 100
        name: Users
        description: User VLAN
        state: present
      vars:
        ansible_network_os: junos
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: netconf
```

This playbook configures an interface on a Cisco switch and creates a VLAN on a Juniper switch.

#### Using Network Modules

Ansible provides various network modules specific to different vendors and general-purpose modules for network automation.

**Example: Using Cisco IOS Module**

Create a playbook named `cisco_config.yml`:

```yaml
---
- name: Configure Cisco IOS device
  hosts: cisco_switch
  gather_facts: no
  tasks:
    - name: Configure hostname
      cisco.ios.ios_system:
        hostname: Switch01
      vars:
        ansible_network_os: ios
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: network_cli

    - name: Configure interface description
      cisco.ios.ios_interface:
        name: GigabitEthernet1
        description: Uplink to Core Router
        enabled: yes
        state: present
      vars:
        ansible_network_os: ios
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: network_cli
```

This playbook sets the hostname and configures an interface description on a Cisco IOS device.

**Example: Using Juniper Junos Module**

Create a playbook named `juniper_config.yml`:

```yaml
---
- name: Configure Juniper Junos device
  hosts: juniper_switch
  gather_facts: no
  tasks:
    - name: Configure hostname
      junipernetworks.junos.junos_system:
        hostname: Switch01
      vars:
        ansible_network_os: junos
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: netconf

    - name: Configure VLAN
      junipernetworks.junos.junos_vlan:
        vlan_id: 100
        name: Users
        description: User VLAN
        state: present
      vars:
        ansible_network_os: junos
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: netconf
```

This playbook sets the hostname and configures a VLAN on a Juniper Junos device.

#### Ansible for Network Automation and Orchestration

Network automation with Ansible involves automating repetitive tasks, ensuring consistency across devices, and integrating with other IT processes. 
Orchestration involves managing and coordinating complex tasks across multiple devices and services.

**Example: Automating Network Configuration**

Create a playbook named `network_automation.yml`:

```yaml
---
- name: Automate network configuration
  hosts: all_network_devices
  gather_facts: no
  tasks:
    - name: Configure hostname on Cisco devices
      cisco.ios.ios_system:
        hostname: "{{ inventory_hostname }}"
      vars:
        ansible_network_os: ios
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: network_cli
      when: ansible_network_os == "ios"

    - name: Configure hostname on Juniper devices
      junipernetworks.junos.junos_system:
        hostname: "{{ inventory_hostname }}"
      vars:
        ansible_network_os: junos
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: netconf
      when: ansible_network_os == "junos"

    - name: Configure VLAN on Cisco devices
      cisco.ios.ios_vlan:
        vlan_id: 100
        name: Users
        description: User VLAN
        state: present
      vars:
        ansible_network_os: ios
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: network_cli
      when: ansible_network_os == "ios"

    - name: Configure VLAN on Juniper devices
      junipernetworks.junos.junos_vlan:
        vlan_id: 100
        name: Users
        description: User VLAN
        state: present
      vars:
        ansible_network_os: junos
        ansible_user: your_username
        ansible_password: your_password
        ansible_connection: netconf
      when: ansible_network_os == "junos"
```

This playbook configures the hostname and VLAN on both Cisco and Juniper devices, demonstrating how Ansible can automate network configuration tasks.

### Conclusion

Ansible provides powerful tools for managing and automating network devices and services. 
By using network-specific modules and creating detailed playbooks, you can streamline network management, 
ensure configuration consistency, and integrate network operations with broader IT workflows. 

The provided examples and detailed explanations should help you effectively use Ansible for network automation and orchestration in your environment.
