- name: PostgreSQL
  hosts: localhost
  become: yes
  gather_facts: false
  tasks:
    - name: Create a new database with name "acme" which has a limit of 100 concurrent connections
      community.postgresql.postgresql_db:
              name: acme
              conn_limit: "100"
              encoding: UTF-8
              template: template0

    - name: Connect to acme database, create django user, and grant access to database and products table
      community.postgresql.postgresql_user:
        db: acme
        role_attr_flags: NOSUPERUSER
        name: django
        password: password
        # priv: "CONNECT/products:ALL"
        expires: "Jan 31 2022"

    - name: Connect to test database, remove test user from cluster
      community.postgresql.postgresql_user:
        db: acme
        role_attr_flags: NOSUPERUSER
        name: django
        priv: ALL
        state: absent

    - name: Create tbl2 table
      community.postgresql.postgresql_table:
        db: acme
        table: tbl2
        tablespace: space2
