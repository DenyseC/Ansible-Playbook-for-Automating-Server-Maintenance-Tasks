### Title: Ansible Playbook for Automating Server Maintenance Tasks

#### Description:
This Ansible playbook automates common server maintenance tasks such as updating packages, checking disk usage, and restarting necessary services. It ensures that servers remain up-to-date and perform optimally.

#### File: `maintenance-playbook.yml`

```yaml
---
- name: Automated Server Maintenance
  hosts: all
  become: yes

  tasks:
    - name: Update all packages to the latest version
      apt:
        update_cache: yes
        upgrade: dist

    - name: Clean up unused packages and cache
      apt:
        autoclean: yes
        autoremove: yes

    - name: Check disk usage
      command: df -h
      register: disk_usage

    - name: Display disk usage
      debug:
        var: disk_usage.stdout_lines

    - name: Restart web server
      service:
        name: apache2
        state: restarted
      when: "'apache2' in ansible_facts.services"

    - name: Restart application server
      service:
        name: your_application_service
        state: restarted
      when: "'your_application_service' in ansible_facts.services"

    - name: Ensure the firewall is enabled and running
      ufw:
        state: enabled
        policy: allow
        rules:
          - { port: '22', proto: 'tcp', from: 'any' }
          - { port: '80', proto: 'tcp', from: 'any' }
          - { port: '443', proto: 'tcp', from: 'any' }

    - name: Check for any failed systemd services
      shell: systemctl --failed
      register: failed_services

    - name: Display failed services
      debug:
        var: failed_services.stdout_lines

```

#### Instructions:
1. Ensure Ansible is installed on your control machine.
2. Create an inventory file `hosts.ini` with the list of servers to manage:

    ```
    [webservers]
    server1.example.com
    server2.example.com

    [appservers]
    server3.example.com
    server4.example.com
    ```

3. Run the playbook with the following command:

    ```bash
    ansible-playbook -i hosts.ini maintenance-playbook.yml
    ```


