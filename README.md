bacula_storage
==============

Ansible role which helps to install and configure Bacula Storage.

The configuration of the role is done in such way that it should not be
necessary to change the role for any kind of configuration. All can be
done either by changing role parameters or by declaring completely new
configuration as a variable. That makes this role absolutely
universal. See the examples below for more details.

Please report any issues or send PR.


Examples
--------

```
---

# Example of how to use the role with default parameters
- hosts: myhost1
  roles:
    - bacula_storage

# Example of how to specify the Directory address
- hosts: myhost2
  vars:
    bacula_storage_config_director_options__custom:
      - Address = 192.168.1.123
  roles:
    - bacula_storage
```

Please refer to the
[`bacula_director`](https://github.com/jtyr/ansible-bacula_director) for more
complete example of Bacula installation.


Role variables
--------------

Variables used by the role is as follows:

```
# Service name
bacula_storage_service: bacula-sd

# Default DB engine (can be 'pgsql' or 'mysql')
bacula_storage_db_engine: pgsql

# Package to be installed (exact version can be specified here)
bacula_storage_pkg: "{{
  'bacula-storage-postgresql'
    if bacula_storage_db_engine == 'pgsql'
    else
  'bacula-storage-mysql' }}"

# Path the to the Storage config file
bacula_storage_config_file_path: /etc/bacula/bacula-sd.conf


# Values of the default Storage options
bacula_storage_config_storage_options_name: bacula-sd
bacula_storage_config_storage_options_sdport: 9103
bacula_storage_config_storage_options_working_directory: /var/spool/bacula
bacula_storage_config_storage_options_pid_directory: /var/run
bacula_storage_config_storage_options_pid_max_jobs: 20

# Default Storage resource options for the first Storage definition
bacula_storage_config_storage_options__default:
  - Name = {{ bacula_storage_config_storage_options_name }}
  - SD Port = {{ bacula_storage_config_storage_options_sdport }}
  - Working Directory = {{ bacula_storage_config_storage_options_working_directory }}
  - Pid Directory = {{ bacula_storage_config_storage_options_pid_directory }}
  - Maximum Concurrent Jobs = {{ bacula_storage_config_storage_options_pid_max_jobs }}

# Custom Storage resource options
bacula_storage_config_storage_options__custom: []

# Default Storage resource options
bacula_storage_config_storage:
  - Storage: "{{
      bacula_storage_config_storage_options__default +
      bacula_storage_config_storage_options__custom }}"


# Default values of Director resource options
bacula_storage_config_director_options_name: bacula-dir
bacula_storage_config_director_options_password: "{{ bacula_director_config_director_options_password | default('bacula') }}"

# Default Director resource options
bacula_storage_config_director_options__default:
  - Name = {{ bacula_storage_config_director_options_name }}
  - Password = {{ bacula_storage_config_director_options_password }}

# Custom Director resource options
bacula_storage_config_director_options__custom: []

# First Director resource
bacula_storage_config_director__default:
  - Director: "{{
      bacula_storage_config_director_options__default +
      bacula_storage_config_director_options__custom }}"

# Variable for additional Director resources (e.g. for monitor)
bacula_storage_config_director__custom: []

# Final Director resource
bacula_storage_config_director: "{{
  bacula_storage_config_director__default +
  bacula_storage_config_director__custom }}"


# Default values of Device resource options
bacula_storage_config_device_options_name: FileStorage
bacula_storage_config_device_options_media_type: File
bacula_storage_config_device_options_archive_device: /tmp
bacula_storage_config_device_options_label_media: 'yes'
bacula_storage_config_device_options_random_access: 'yes'
bacula_storage_config_device_options_automatic_mount: 'yes'
bacula_storage_config_device_options_removable_media: 'no'
bacula_storage_config_device_options_always_open: 'no'

# Default Device resource options
bacula_storage_config_device_options__default:
  - Name = {{ bacula_storage_config_device_options_name }}
  - Media Type = {{ bacula_storage_config_device_options_media_type }}
  - Archive Device = {{ bacula_storage_config_device_options_archive_device }}
  - Label Media = {{ bacula_storage_config_device_options_label_media }}
  - Random Access = {{ bacula_storage_config_device_options_random_access }}
  - Automatic Mount = {{ bacula_storage_config_device_options_automatic_mount }}
  - Removable Media = {{ bacula_storage_config_device_options_removable_media }}
  - Always Open = {{ bacula_storage_config_device_options_always_open }}

# Custom Device resource options
bacula_storage_config_device_options__custom: []

# First Device resource
bacula_storage_config_device__default:
  - Device: "{{
      bacula_storage_config_device_options__default +
      bacula_storage_config_device_options__custom }}"

# Variable for additional Device resources
bacula_storage_config_device__custom: []

# Final Device resource
bacula_storage_config_device: "{{
  bacula_storage_config_device__default +
  bacula_storage_config_device__custom }}"


# Default values of Messages resource options
bacula_storage_config_messages_options_name: Standard
bacula_storage_config_messages_options_director_name: bacula-dir
bacula_storage_config_messages_options_director: "{{ bacula_storage_config_messages_options_director_name }} = all"

# Default Messages resource options
bacula_storage_config_messages_options__default:
  - Name = {{ bacula_storage_config_messages_options_name }}
  - Director = {{ bacula_storage_config_messages_options_director }}

# Custom Messages resource options
bacula_storage_config_messages_options__custom: []

# First Messages resource
bacula_storage_config_messages__default:
  - Messages: "{{
      bacula_storage_config_messages_options__default +
      bacula_storage_config_messages_options__custom }}"

# Variable for additional Messages resources
bacula_storage_config_messages__custom: []

# Final Messages resource
bacula_storage_config_messages: "{{
  bacula_storage_config_messages__default +
  bacula_storage_config_messages__custom }}"


# Custom resources
bacula_storage_config_custom: []


# Path to the sysconfig file
bacula_storage_sysconfig_file: /etc/sysconfig/bacula-sd

# Default values of the default sysconfig options
bacula_storage_sysconfig_user: "bacula"
bacula_storage_sysconfig_group: "tape"

# Default sysconfig options
bacula_storage_sysconfig__default:
  SD_USER: "{{ bacula_storage_sysconfig_user }}"
  SD_GROUP: "{{ bacula_storage_sysconfig_group }}"

# Custom sysconfig options
bacula_storage_sysconfig__custom: {}

# Final sysconfig
bacula_storage_sysconfig: "{{
  bacula_storage_sysconfig__default.update(bacula_storage_sysconfig__custom) }}{{
  bacula_storage_sysconfig__default }}"
```


Dependencies
------------

- [`bacula_client`](https://github.com/jtyr/ansible-bacula_client) (optional)
- [`bacula_console`](https://github.com/jtyr/ansible-bacula_console) (optional)
- [`bacula_director`](https://github.com/jtyr/ansible-bacula_director) (optional)
- [`config_encoder_filters`](https://github.com/jtyr/ansible-config_encoder_filters)


License
-------

MIT


Author
------

Jiri Tyr
