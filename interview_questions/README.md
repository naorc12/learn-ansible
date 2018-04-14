# Interview Questions

This file contains several Ansible interview questions

## Ansible Newbie


* Describe each of the following components in Ansible, including the relationship between them:

    Task
    Module
    Play
    Playbook
    Role

```
Task - a call to a specific Ansible module

Module - the actual unit of code executed by Ansible on your own host or a remote host. Modules are indexed by category (database, file, network, ...) and also referred as task plugins.

Play - One or more tasks executed on a given host(s)

Playbook - One or more plays. Each play can be executed on the same or different hosts

Role - playbook doesn't scale. When you want to add variables, templates, file, and handlers, your playbook starts to be unreadable and impossible to reuse in other projects.
       This is why Ansible roles were created. Ansible role allows you to group resources based on certain functionality/service and easily reuse it.
       In a role, you have directories for variables, defaults, files, templates, handlers, tasks, and metadata. You can then use the role by simply specifying it in your playbook.
```

* Write a task to create the directory '/tmp/new_directory'

```
- name: Create a new directory
  file:
      path: "/tmp/mario"
      state: directory
```

* What would be the result of the following play?

- name: Print information about my host
  hosts: localhost
  gather_facts: 'no' 
  tasks:
      - name: Print hostname
        debug:
            msg: "It's a me, {{ ansible_hostname }}"

```
If your answer is "this will fail" then you are right :)
```

* Write a playbook to install 'zlib' and 'openssl-devel' on all hosts if the file '/tmp/mario' exists on the system.

```
- hosts: all
  vars:
      mario_file: /tmp/mario
  tasks:
      - name: Check for mario file
        stat:
            path: "{{ mario_file }}"
        register: mario_f

      - name: Install zlib and openssl-devel if mario file exists
        become: "yes"
        package:
            name: "{{ item }}"
            state: present
        with_items:
            - 'zlib'
            - 'openssl-devel'
        when: mario_f.stat.exists
```

* Write a playbook to deploy the file '/etc/system_info' on all hosts except for controllers group, with the following content

  `I'm <HOSTNAME> and my operating system is <OS>`
  
  replace <hostname> and  <OS> with the actual data for the specific host you are running on

```
- name: Deploy /tmp/system_info file
  hosts: all:!controllers
  tasks: 
      - name: Deploy /tmp/system_info
        template:
            src: system_info.j2 
            dest: /tmp/system_info
```

* How would you change the output format of Ansible execution to display only the name of the tasks?

```
The answer is "callback plugin". It doesn't let you to change only what you see but basically do everything you want
based on different events in Ansible. So a similar question might be: "how would you log everything to a file?" and the
answer would still be a callback plugin.

Here some of the main points you need to know about callback plugins:

- There are several callback plugins in Ansible tree
- To enable callback plugin, include it in ansible.cfg like this:
  callback_whitelist = my_cool_plugin
- To change the default stdout of Ansible execution, modify ansible.cfg to include
  stdout_callback = my_new_stdout_callback_plugin
```

* File '/tmp/exercise' includes the following content

Goku = 9001
Vegeta = 5200
Trunks = 6000
Gotenks = 32

With one task, switch the content to:

Goku = 9001
Vegeta = 250
Trunks = 40
Gotenks = 32

```
- name: Change saiyans level
  lineinfile:
      dest: /tmp/exercise
      regexp: "{{ item.regexp }}"
      line: "{{ item.line }}"
  with_items:
      - { regexp: '^Vegeta', line: 'Vegeta = 250' }
      - { regexp: '^Trunks', line: 'Trunks = 40' }
```

* You have the following play

- name: Print variable    
  hosts: localhost    
  tasks:         
      - name: Print test variable
        debug:    
            msg: "{{ test }}"

And you run the following command

ansible-playbook -e test="test 1 2 3" debug.yaml

What would be the output of the test variable?

test 1 2 3
test
empty string
the variable is not defined

```
The answer is 'test'.

This one is tricky and it checks whether you understand shell and Ansible.
In this case, since the command is first processed by the shell, whatever in the
quotes and after a backslash or space will be ignored.
```
