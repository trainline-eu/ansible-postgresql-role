## PostgreSQL Ansible role [![Build Status](https://travis-ci.org/trainline-eu/ansible-postgresql-role.svg?branch=master)](https://travis-ci.org/trainline-eu/ansible-postgresql-role)

Ansible role which installs and configures PostgreSQL clusters, databases and users.

See [role page on Ansible Galaxy](https://galaxy.ansible.com/trainline-eu/ansible_postgresql_role).

#### Installation

This role has been tested on Ansible 2.5.0 and higher.

To install:

```
ansible-galaxy install trainline-eu.ansible_postgresql_role
```

#### Dependencies

No dependencies

Recommended dependencies:
- [trainline-eu.ansible_barman_role](https://galaxy.ansible.com/trainline-eu/ansible_barman_role)

#### Compatibility matrix

This table lists the tested version of OS/PostgreSQL couples.

| Distribution / PostgreSQL | 9.1                           | 9.4                           | 9.5                | 9.6                | 10                 | 11                 | 12                 |
| ------------------------- | :---:                         | :---:                         | :---:              | :---:              | :---:              | :---:              | :---:              |
| Debian 8.x                | :no_entry: :white_check_mark: | :no_entry: :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |
| Debian 9.x                | :no_entry: :white_check_mark: | :no_entry: :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: | :white_check_mark: |

- :white_check_mark: - tested, works fine
- :grey_question: - will work in the future (help out if you can)
- :interrobang: - maybe works, not tested
- :no_entry: - PostgreSQL has reached EOL

#### Variables

```yaml
# Basic settings
postgres_listen_addresses: [ '127.0.0.1' ] # Optional
postgres_log_dir: '/home/postgres-logs'    # Optional
postgres_data_dir: '/home/postgres'        # Optional
postgres_clusters:                         # Mandatory
  - version: 11                              # Mandatory
    name: 'main'                             # Mandatory
    port: 5432                               # Mandatory
    checksums: True                          # Optional
    fsync_enabled: False                     # Optional
    archive_enabled: False                   # Optional
    wal_level: 'logical'                     # Optional
    max_replication_slots: 10                # Optional
    barman_directory: None                   # Optional
    # Define cluster as a standby server
    primary:                                 # Optional
      host: '127.0.1.1'                        # Mandatory
      port: 5433                               # Mandatory
      replication_user:     'replicator'       # Mandatory
      replication_password: 'SuperSecret'      # Mandatory
      restore_command: None                    # Optional
      restore_barman_directory: None           # Optional
    # List of users to be created (optional)
    users:
      - username: 'replicator'                 # Mandatory
        password: 'SuperSecret'                # Mandatory
        permissions: 'REPLICATION'             # Mandatory
    # List of databases to be created (optional)
    databases:
      - dbname: my_database                    # Mandatory
        owner: john                            # Mandatory
        extensions:                            # Optional
          - names: [ 'postgis', 'postgis_topology' ]
            apt_deps: [ 'postgresql-11-postgis' ]

# Postgres config (Optional)
postgres_log_line_prefix: '%m [%p] database: %d host: %h user: %u '
postgres_datestyle: 'iso, dmy'
postgres_locale_formats: fr_FR.UTF-8
postgres_text_search_config: pg_catalog.french

# Postgres pg_hba config (optional)
postgres_allowed_hosts:
  - user: all
    range: 10.0.0.0/24
postgres_replication_hosts:
  - user: replicator
    range: 10.0.0.0/24

# Ansible related Configuration
postgres_become_method: su  # Optional

# Barman connectivity
postgres_barman_server: barman.example.com  # Required if at least one server has archive_enabled enabled
postgres_barman_rsync_enabled: False        # Optional
postgres_barman_rsync_options: ''           # Optional
postgres_barman_remote_user: barman         # Optional
postgres_barman_path_prefix: '~'            # Optional, required if using rsync
```

#### Testing

This project comes with a `test/main.yml` testing playbook. It uses `Docker` to provision containers locally and sets up a 3 node postgresql cluster with a barman server.

Coverage of this playbook is probably not complete but at least it's there.

If you are contributing, please first test your changes in a new playbook in the `test/` directory within docker containers, (using the targeted distribution), and if possible, ensure your change is covered in the tests found in [.travis.yml](./.travis.yml).

#### License

Licensed under the MIT License. See the [LICENSE](./LICENSE) file for details.


#### Thanks

Creators:
- [Gaëtan Duchaussois](https://twitter.com/gduchaussois)
- [Théophile Helleboid](https://twitter.com/chtitux)
- [Paul Bonaud](https://twitter.com/paulRb_r)

Maintainers:
- [Théophile Helleboid](https://twitter.com/chtitux)
- [Paul Bonaud](https://twitter.com/paulRb_r)

#### Feedback, bug-reports, requests, ...

Are [welcome](https://github.com/trainline-eu/postgresql/issues)!
