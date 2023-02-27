#### 각 노드의 특정 프로 세스 죽이기

```
- name: playbook test
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  become_user: root
  gather_facts: false
  vars:
    - ansible_user: sm55555
  
  tasks:
  - name: test
    shell: ps -ef | grep temp_process | grep -v grep | aws '{print $2}'
    register: output
  
  - name: kill_process
    shell: "kill {{output.stdout_liens[0]}}"
  
  - debug:
      var: output.stdout_liens
```
