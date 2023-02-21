## simplecommand

```
ansible-playbook -i ./testsample simple_command.yml -k -K
```

#### 각 노드에 ls 명령어 내려보기


```yml
- name: playbook test
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  #become_user: root   ->  root로 유저 변경
  gather_facts: false
  vars:
    - ansible_user: sm55555
  
  tasks:
  - name: test
    shell: ls
    register: output
    
  - debug:
      var: output.stdout_liens
  
```

각 노드에 파일 압축하기
