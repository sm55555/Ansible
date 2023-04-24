#### 각 노드의 파일 보내기

src,dest가 존재해야한다.

```yml
- name: playbook test
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  become_user: root
  gather_facts: false
  vars:
    - ansible_user: sm55555
  
  tasks:
  - name: test
   copy:
     src: /home/testuser/temp.txt
     dest: /home/testuser/temp.txt
  - debug:
      var: output.stdout_liens
```
