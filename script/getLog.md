### 로그 정재

#### 각 노드에 master (passwd 권한이 있는 계정)을 통한 test 유저 비번 초기화 ( 왜냐하면 만료된 계정이 있는 서버가 존재할 수 있다.
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

#### 정상적으로 변경되어있는데 막상들어가보면 여러번 비밀번호 실패가 확인되는 서버도 있다.... pam_tally2 설정을 해주자
master (pam_tally2 권한이 있는 계정)을 통한 test pam_tally2 초기화

```yml
- name: playbook test
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  become_method: sudo
  gather_facts: false
  vars:
    - ansible_user: master
      
  tasks:
  - name: pam_tally reset
    shell: pam_tally2 -u master -r
    register: output
  - name: Insert pam_tally2.so rule in system-auth
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

### 활용해서 ansible_host만들기

아래와 같은 ASG를 ansible_host 파일로만들 경우

```
| aws-test-asg           | 111.11.20.15 |
| aws-test-asg           | 111.11.20.16 |
| aws-test-asg           | 111.11.20.17 |
| aws-test-asg           | 111.11.21.21 |
| aws-test-asg           | 111.11.21.22 |
| aws-test-asg           | 111.11.21.23 |
| aws-test-asg           | 111.11.22.31 |
| aws-test-asg           | 111.11.22.32 |
| aws-test-asg           | 111.11.22.33 |


명령어 출력문 | awk '{ print $2NR" ansible_host="$4}'

aws-test-asg1 ansible_host=111.11.20.15
aws-test-asg2 ansible_host=111.11.20.16
aws-test-asg3 ansible_host=111.11.20.17
...
aws-test-asg9 ansible_host=111.11.22.33
```


#### ansible_host에 있는 정보를 이용하여 log 압축 및 fetch

```yml
- name: playbook test
  hosts: [hosts 파일 상단의 [내용]]
  become: true
  become_method: sudo
  gather_facts: false
  vars:
    - ansible_user: test
      
  tasks:
  - name: File compression
    shell: tar czvf "{{ ansible_host }}"-0423.tgz /app/log/log-20230423*
    register: output
    
  - name: Fetch File
    fetch:
     src: "{{ ansible_host }}-0423.tgz"
     dest: /user/home/test
     flat: true
    register: output
  
  - debug:
      var: output.stdout_liens
```

