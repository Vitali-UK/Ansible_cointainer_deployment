---
- name: Deploy Blazor app
  hosts: web
  gather_facts: true
  vars:
    app_name: myblazorapp
    app_folder: /var/www/{{ app_name }}
    app_port: 5000
    nginx_config_file: /etc/nginx/sites-available/{{ app_name }}.conf
    cert_path: /etc/ssl/certs/myblazorapp.crt
    key_path: /etc/ssl/private/myblazorapp.key
    git_repo: https://github.com/user/myblazorapp.git
    git_branch: master
  tasks:
  - name: Install .NET Core SDK
    apt:
      name: dotnet-sdk-3.1
      state: present

  - name: Create app folder
    file:
      path: "{{ app_folder }}"
      state: directory
      mode: 0755
      owner: www-data
      group: www-data

  - name: Clone the Git repository
    git:
      repo: "{{ git_repo }}"
      dest: "{{ app_folder }}"
      version: "{{ git_branch }}"

  - name: Build and publish Blazor app
    command: dotnet publish -c release -o "{{ app_folder }}/publish"
    args:
      chdir: "{{ app_folder }}"

  - name: Install and configure Nginx
    apt:
      name: nginx
      state: present
  - name: Remove default nginx config
    file:
      path: /etc/nginx/sites-available/default
      state: absent
  - name: Create Nginx config file
    template:
      src: nginx.conf.j2
      dest: "{{ nginx_config_file }}"
  - name: Enable Nginx config file
    file:
      src: "{{ nginx_config_file }}"
      dest: /etc/nginx/sites-enabled/{{ app_name }}.conf
  - name: Start the Nginx service
    service:
      name: nginx
      state: started
      enabled: yes
