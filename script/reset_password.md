#### 각 노드에 master (passwd 권한이 있는 계정)을 통한 test 유저 비번 초기화

master 유저 비번 (Password1!)
test 유저 비번 (test1!)

```yml
- name: Change Password
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  become_method: sudo
  gather_facts: false
  vars:
    - ansible_user: master
    - ansible_password: "Password1!"
    - ansible_become_password: "{{ ansible_pasword }}"
    - username: "test"
    - newpasswd: "test1!"
      
  tasks:
  - name: Checking if {{ username }} user exist
    user :
      name: "{{ username }}"
      state: present
    check_mode: yes
    register: status
    
  - name: update password on servers which have the user named {{ username }}
    user :
      name: "{{ username }}"
      update_password: always  
      password: "{{newpasswd | password_hash('sha512') }}"    
    when: status.changed == false
```
