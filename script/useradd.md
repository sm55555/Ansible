## Creating Users in Specific Group

```yml
- name: playbook test
  hosts: HOSTNAME
  become: true
  become_method: sudo
  #become_user: root
  gather_facts: fasle
  vars:
    - ansible_user: sm55555
    # new user initial password
    - newpasswd: "dhtkdals"
  
  tasks:
  - name: Checking If sm55555 user exist
    user:
      name: "sm55555"
      state: present
    check_mode: yes
    register: status
   
  - name : Get the last uid from /etc/passwd
    shell: cat /etc/passwd | grep -v nms | aws -F ":" '{print $3}' | grep -E '^6.{2}$' | sort | tail -1
    register : output
    when: status is chagned
    
  - name : Create a user named {{ username}}
    user:
      name: "sm55555"
      # Password Encryption
      password: "{{ newpasswd | password_hash('sha_512') }}"
      uid: "{{ output.stdout_lines[0]} | int +1 }}"
      group: DEV
      shell: /bin/bash
      home: '/home/_user/sm55555'
    when: status is changed
    
   - name: expire user
     shell: chage -d 0 sm55555
     
   - debug:
       var: output
    

```


