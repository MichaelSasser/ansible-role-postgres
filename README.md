# Ansible Role: PostgreSQL

An Ansible role, which installs and manages a PostgreSQL Docker container.

## Requirements

This role doesn't install Docker on the host. You need to make sure the docker
is installed and running on your system. You can do that e.g. with [ericsysmin's collection](https://galaxy.ansible.com/ericsysmin/docker) (which only contains the docker role).

## Role Variables


| Variable                           | Default Value                           | Description                                                                       |
| ---------------------------------- | --------------------------------------- | --------------------------------------------------------------------------------- |
| `postgres_docker_image`            | postgres                                | The docker image, which is used to pull the container.                            |
| `postgres_docker_image_tag`        | 13-alpine                               | The tag of the image.                                                             |
| `postgres_container_name`          | postgres                                | The container name.                                                               |
| `postgres_port`                    | 5432                                    | The port postgres will be available on the host.                                  |
| `postgres_networks`                | `- name: "{{ docker_network_name }}"`   | The network the container will be connected to.                                   |
| `postgres_container_memory_limit`  | null                                    | A memory limit for the container                                                  |
| `postgres_upgrade`                 | no                                      | if set to `yes` the container will be updated to the latest version (of the tag). |
| `postgres_system_postgres_disable` | no                                      | If yes, any version of postgres running outside of the containe will be disabled. |
| `postgres_password`                |                                         | The password for the postgres user.                                               |
| `postgres_roles`                   |                                         | roles (users), which will be created by the playbook. (see below)                 |
| `postgres_databases`               |                                         | databases, which will be created by the playbook. (see below)                     |

### PostgeSQL Roles (Users)

#### PostgreSQL Role Variables

| Variable          | Default             | Choises                                                                                 | Description                                                                                                                                                                                                                                                                                                                                                                                                                                  |
| ----------------- | ------------------- | --------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `role`            |                     |                                                                                         | The name of the role (username).                                                                                                                                                                                                                                                                                                                                                                                                             |
| `password`        |                     |                                                                                         | The password of the role.                                                                                                                                                                                                                                                                                                                                                                                                                    |
| `role_attr_flags` | `NOSUPERUSER,LOGIN` | `SUPERUSER`, `CREATEROLE`, `CREATEDB`, `INHERIT`, `LOGIN`, `REPLICATION`, `BYPASSRLS`   | PostgreSQL user attributes string in the format: CREATEDB,CREATEROLE,SUPERUSER. (negat, by adding a NO in font of the choices)                                                                                                                                                                                                                                                                                                               |
| `priv`            |                     |                                                                                         | Slash-separated PostgreSQL privileges string: priv1/priv2, where you can define the user's privileges for the database ( allowed options - 'CREATE', 'CONNECT', 'TEMPORARY', 'TEMP', 'ALL'. For example CONNECT ) or for table ( allowed options - 'SELECT', 'INSERT', 'UPDATE', 'DELETE', 'TRUNCATE', 'REFERENCES', 'TRIGGER', 'ALL'. For example table:SELECT ). Mixed example of this string: CONNECT/CREATE/table1:SELECT/table2:INSERT. |
| `comment`         |                     |                                                                                         | A simple comment to the user.                                                                                                                                                                                                                                                                                                                                                                                                                |
| `state`           | `present`           | `present`, `absent`                                                                     | `present` means create/modify role. `absent` means delete the role                                                                                                                                                                                                                                                                                                                                                                           |
| `expires`         | `infinity`          |                                                                                         | Set a date when the user's password expires.                                                                                                                                                                                                                                                                                                                                                                                                 |

#### Example: Create roles

The `postgres_roles` are created like this:
```yaml
---
# .../inventory/host_vars/hostname.domain.tld/vars.yml

# Create the roles "userfoo" and "userbar"
postgres_roles:
  - role: userfoo
    password: "secret_foo"
    comment: Just a foo user
    role_attr_flags: CREATEDB,NOSUPERUSER,LOGIN
    priv:
    expires:
    state: present
  - role: userbar
    password: "secret_bar"
    comment: Just a bar user
    role_attr_flags: NOSUPERUSER,LOGIN
```

### PostgeSQL Databases

#### PostgreSQL Database Variables

| Variable     | Default             | Choises                | Description                                                         |
| ------------ | ------------------- | ---------------------- | ------------------------------------------------------------------- |
| `database`   |                     |                        | The name of the database.                                           |
| `owner`      |                     |                        | The owner (role) of the database.                                   |
| `conn_limit` | `100`               |                        | The maximal amount of active connections to the database.           |
| `state`      | `present`           | `present`, `absent`    | `present` means create/modify role. `absent` means delete the role. |

#### Example: Create Databases

PostgreSQL databases are created with the `postgres_databases` variable like:

```yaml
---
# .../inventory/host_vars/hostname.domain.tld/vars.yml

postgres_databases:
  - database: foo
    owner: userfoo
    conn_limit: 100
    state: present
  - database: bar
    owner: userbar

```


## Example Playbook

Including an example of how to use your role (for instance, with variables passed in as parameters) is always nice for users too:

```yaml
---

- hosts: postgres
  become: true
  roles:
    - michaelsasser.postgres
```

## License

Copyright &copy; 2021 Michael Sasser <Info@MichaelSasser.org>. Released under
the GPLv3 license.
