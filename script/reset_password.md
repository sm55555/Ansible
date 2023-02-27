#### 각 노드에 master (passwd 권한이 있는 계정)을 통한 test 유저 비번 초기화 (Password1!)


```yml
- name: playbook test
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  become_method: sudo
  gather_facts: false
  vars:
    - ansible_user: master
    - newpasswd: "Password1!"
      
  tasks:
  - name: test
    user :
      name: "test"
      update_password: always  
      password: "{{newpasswd | password_hash('sha512') }}"
    
  - debug:
      var: output.stdout_liens
  
```
