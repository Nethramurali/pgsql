---
  - name: PostgreSQL
    hosts: localhost
    become: yes
    vars:
    vars_prompt:
        - name: create_user
          prompt: Enter the value of createuser
          private: no

    gather_facts: false
    tasks:

      - block:
          - name: Install repository
            yum:
              name: https://download.postgresql.org/pub/repos/yum/reporpms/EL-8-x86_64/pgdg-redhat-repo-latest.noarch.rpm
              state: present

          - name: Install PostgreSQ 13
            command: sudo yum install -y postgresql13-server

          - name: Install PostgreSQL 11
            command: sudo yum install -y postgresql11-server

          - name: Install PostgreSQL Contribute
            command: sudo yum install -y  postgresql-contrib

          - name: Create a new PostgreSQL database cluster with initdb
            command: sudo /usr/pgsql-11/bin/postgresql-11-setup initdb
            register: test_out
          - debug: var=test_out

          - name: Start PostgreSQL services
            command: sudo systemctl start postgresql-11

          - name: enable PostgreSQL services
            command: sudo systemctl enable postgresql-11

          - name: To check the status of PostgreSQL services
            command: systemctl status postgresql-11.service
            register: test_out1
          - debug: var=test_out1

          - name: pip install postgres devel
            command: sudo yum install -y postgresql-devel

          - name: pip install postgres libs
            command: sudo yum install postgresql-libs

          - name: pip install psycopg2 for postgres
            command: sudo yum install -y python-psycopg2

          - name: installing the ansible postgres modeule
            command: ansible-galaxy collection install community.postgresql

        ignore_errors: yes

      - block:
          - name: Create super user
            command: sudo -u postgres createuser --interactive

          - name: create db
            command: sudo -u postgres createdb {{create_user}}

          - name: Add user
            command: sudo adduser {{create_user}}

          - name: Execute psql terminal
            command: sudo -u {{create_user}} psql
