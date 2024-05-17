```yaml
- name: Rollback Apache SSL
  hosts: AWS
  become: true
  become_method: sudo
  gather_facts: false
  vars:
    - ansible_user: sm55555

# ssl.conf 내에 2024 폴더에서 2023 로 바라보게 설정
  tasks:
  - name: Replace string /etc/httpd/conf.d/ssl.conf
    replace:
      path: /etc/httpd/conf.d/ssl.conf
      regexp: "ssl_2024/star_m_bithumb_com_cert.pem"
      replace: "ssl_2023/star_m_bithumb_com_cert.pem"

  - name: Replace string /etc/httpd/conf.d/ssl.conf
    replace:
      path: /etc/httpd/conf.d/ssl.conf
      regexp: "ssl_2024/star_m_bithumb_com_key.pem"
      replace: "ssl_2023/star_m_bithumb_com_key.pem"

  - name: Replace string /etc/httpd/conf.d/ssl.conf
    replace:
      path: /etc/httpd/conf.d/ssl.conf
      regexp: "ssl_2024/Chain_RootCA_Bundle_m.crt"
      replace: "ssl_2023/Chain_RootCA_Bundle_m.crt"

# seo.conf 내에 2024 폴더에서 2023 로 바라보게 설정
  - name: Replace string /etc/httpd/conf.d/seo.conf
    replace:
      path: /etc/httpd/conf.d/seo.conf
      regexp: "ssl_2024/star_m_bithumb_com_cert.pem"
      replace: "ssl_2023/star_m_bithumb_com_cert.pem"
  - name: Replace string /etc/httpd/conf.d/seo.conf
    replace:
      path: /etc/httpd/conf.d/seo.conf
      regexp: "ssl_2024/star_m_bithumb_com_key.pem"
      replace: "ssl_2023/star_m_bithumb_com_key.pem"
  - name: Replace string /etc/httpd/conf.d/seo.conf
    replace:
      path: /etc/httpd/conf.d/seo.conf
      regexp: "ssl_2024/Chain_RootCA_Bundle_m.crt"
      replace: "ssl_2023/Chain_RootCA_Bundle_m.crt"

# alpha.conf 내에 2024 폴더에서 2023 로 바라보게 설정
  - name: Replace string /etc/httpd/conf.d/alpha.conf
    replace:
      path: /etc/httpd/conf.d/alpha.conf
      regexp: "ssl_2024/star_m_bithumb_com_cert.pem"
      replace: "ssl_2023/star_m_bithumb_com_cert.pem"
  - name: Replace string /etc/httpd/conf.d/alpha.conf
    replace:
      path: /etc/httpd/conf.d/alpha.conf
      regexp: "ssl_2024/star_m_bithumb_com_key.pem"
      replace: "ssl_2023/star_m_bithumb_com_key.pem"
  - name: Replace string /etc/httpd/conf.d/alpha.conf
    replace:
      path: /etc/httpd/conf.d/alpha.conf
      regexp: "ssl_2024/Chain_RootCA_Bundle_m.crt"
      replace: "ssl_2023/Chain_RootCA_Bundle_m.crt"      
      

# 만약 apachectl configtest 결과가 Syntax OK 아니라면 httpd 재기동 하지 않음
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
