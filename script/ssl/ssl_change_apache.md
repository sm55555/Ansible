
```yaml
- name: Change Apache SSL
  hosts: AWS
  become: true
  become_method: sudo
  gather_facts: false
  vars:
    - ansible_user: sm55555

  tasks:
# 2024 폴더 생성
  - name: Create a directory
    file:
      path: /etc/httpd/conf/ssl_2024
      state: directory

# 2024 local에서 인증서 서버갱신 서버로 파일 전송
  - name: copy a file from local
    copy:
      src: /home/_user/sm55555/ssl/2024/apache/Chain_RootCA_Bundle_m.crt
      dest: /etc/httpd/conf/ssl_2024/Chain_RootCA_Bundle_m.crt

  - name: copy a file from local
    copy:
      src: /home/_user/sm55555/ssl/2024/apache/star_m_Google_com_cert.pem
      dest: /etc/httpd/conf/ssl_2024/star_m_Google_com_cert.pem
 
  - name: copy a file from local
    copy:
      src: /home/_user/sm55555/ssl/2024/apache/star_m_Google_com_key.pem
      dest: /etc/httpd/conf/ssl_2024/star_m_Google_com_key.pem

# ssl.conf 내에 2023 폴더에서 2024 로 바라보게 설정
  - name: Replace string /etc/httpd/conf.d/ssl.conf
    replace:
      path: /etc/httpd/conf.d/ssl.conf
      regexp: "ssl_2023/star_m_Google_com_cert.pem"
      replace: "ssl_2024/star_m_Google_com_cert.pem"
  
  - name: Replace string /etc/httpd/conf.d/ssl.conf
    replace:
      path: /etc/httpd/conf.d/ssl.conf
      regexp: "ssl_2023/star_m_Google_com_key.pem"
      replace: "ssl_2024/star_m_Google_com_key.pem"

  - name: Replace string /etc/httpd/conf.d/ssl.conf
    replace:
      path: /etc/httpd/conf.d/ssl.conf
      regexp: "ssl_2023/Chain_RootCA_Bundle_m.crt"
      replace: "ssl_2024/Chain_RootCA_Bundle_m.crt"

# seo.conf 내에 2023 폴더에서 2024 로 바라보게 설정
  - name: Replace string /etc/httpd/conf.d/seo.conf
    replace:
      path: /etc/httpd/conf.d/seo.conf
      regexp: "ssl_2023/star_m_Google_com_cert.pem"
      replace: "ssl_2024/star_m_Google_com_cert.pem"
  - name: Replace string /etc/httpd/conf.d/seo.conf
    replace:
      path: /etc/httpd/conf.d/seo.conf
      regexp: "ssl_2023/star_m_Google_com_key.pem"
      replace: "ssl_2024/star_m_Google_com_key.pem"
  - name: Replace string /etc/httpd/conf.d/seo.conf
    replace:
      path: /etc/httpd/conf.d/seo.conf
      regexp: "ssl_2023/Chain_RootCA_Bundle_m.crt"
      replace: "ssl_2024/Chain_RootCA_Bundle_m.crt"

# alpha.conf 내에 2023 폴더에서 2024 로 바라보게 설정
  - name: Replace string /etc/httpd/conf.d/alpha.conf
    replace:
      path: /etc/httpd/conf.d/alpha.conf
      regexp: "ssl_2023/star_m_Google_com_cert.pem"
      replace: "ssl_2024/star_m_Google_com_cert.pem"
  - name: Replace string /etc/httpd/conf.d/alpha.conf
    replace:
      path: /etc/httpd/conf.d/alpha.conf
      regexp: "ssl_2023/star_m_Google_com_key.pem"
      replace: "ssl_2024/star_m_Google_com_key.pem"
  - name: Replace string /etc/httpd/conf.d/alpha.conf
    replace:
      path: /etc/httpd/conf.d/alpha.conf
      regexp: "ssl_2023/Chain_RootCA_Bundle_m.crt"
      replace: "ssl_2024/Chain_RootCA_Bundle_m.crt"


# 변경된 ssl.conf apachectl configtest 검증
  - name: Run apachectl configtest
    command: apachectl configtest
    register: configtest_output
    ignore_errors: true


# 만약 apachectl configtest 결과가 Syntax OK 아니라면 httpd 재기동 하지 않음
  - name: Restart httpd if configuration is OK
    systemd:
      name: httpd
      state: restarted
    when: "'Syntax OK' in configtest_output.stderr"

```
