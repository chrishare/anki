## Terminology

Source = https://www.digitalocean.com/community/tutorials/configuration-management-101-writing-ansible-playbooks

%

Control Node: the machine where Ansible is installed, responsible for running the provisioning on the servers you are managing.
Inventory: an INI file that contains information about the servers you are managing.
Playbook: a YAML file containing a series of procedures that should be automated.
Task: a block that defines a single procedure to be executed, e.g.: install a package.
Module: a module typically abstracts a system task, like dealing with packages or creating and changing files. Ansible has a multitude of built-in modules, but you can also create custom ones.
Role: a set of related playbooks, templates and other files, organized in a pre-defined way to facilitate reuse and share.
Play: a provisioning executed from start to finish is called a play.
Facts: global variables containing information about the system, like network interfaces or operating system.
Handlers: used to trigger service status changes, like restarting or reloading a service.

## Simple Program

Source = https://www.digitalocean.com/community/tutorials/configuration-management-101-writing-ansible-playbooks

%

Full Example

```
    ---
    - hosts: all
      become: true
      vars:
        doc_root: /var/www/example
      tasks:
        - name: Update apt
          apt: update_cache=yes

        - name: Install Apache
          apt: name=apache2 state=latest

        - name: Create custom document root
          file: path={{ doc_root }} state=directory owner=www-data group=www-data

        - name: Set up HTML file
          copy: src=index.html dest={{ doc_root }}/index.html owner=www-data group=www-data mode=0644

        - name: Set up Apache virtual host file
          template: src=vhost.tpl dest=/etc/apache2/sites-available/000-default.conf
          notify: restart apache
      handlers:
        - name: restart apache
          service: name=apache2 state=restarted
```