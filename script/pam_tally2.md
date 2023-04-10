#### pam_tally2 해체하기

##### 사전 조건

```
accountmaster 계정은 /etc/sudoers.d

accountmaster ALL=PASSWD: /usr/sbin/pam_tally2 이 있어야한다.

```


```yml
- name: playbook test
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  become_method: sudo
  #become_user: root
  gather_facts: false
  vars:
    - ansible_user: accountmaster
  
  tasks:
  - name: test
    shell: pam_tally2 -u [계정 해체 하고 싶은 ID] -r
    register: output
    
  - name:if error happend "Incorrect password"
    pamd:
      name: system-auth
      type: account
      control: required
      module_path: pam_unix.so
      new_type: account
      new_control: required
      new_module_path: pam_tally2.so
      state: after
    
  - debug:
      var: output.stdout_liens
  
```
