# auditd (Ansible role)
-----------------------

This role installs and maintains update auditd and auditd rules.

## Requirements
---------------

This role is tested with `Ansible 2.9.0`

## Variables
------------

| Name                             |               Default                | Description                                                                                                                                                                                                                |
| :------------------------------- | :----------------------------------: | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `auditd_conf_path`               |              /etc/audit              | Path where all configuration and rules are stored                                                                                                                                                                          |
| `auditd_rules_directory`         |    {{ auditd_conf_path }}/rules.d    | Auditd's rules directory mostly under conf_path                                                                                                                                                                            |
| `auditd_plugins_directory`       |   {{ auditd_conf_path }}/plugins.d   | Auditd's plugins directory mostly under conf_path                                                                                                                                                                          |
| `auditd_priority_boost`          |                  4                   | Non-Negativ number to configure priority_boost                                                                                                                                                                             |
| `auditd_name_format`             |                 none                 | How node names are insterted into audit records Possible values: none, hostname, fqd, numeric, user                                                                                                                        |
| `auditd_name`                    |                  ""                  | Static string used when auditd_name_format is user                                                                                                                                                                         |
| `auditd_verify_email`            |                 yes                  | Verifies email address by resolving the domain part                                                                                                                                                                        |
| `auditd_action_mail_acct`        |                 root                 | Mail address to send notifications if actions are happened                                                                                                                                                                 |
| `auditd_local_events`            |                 yes                  | Change only to false, if you want to collect auditd logs only from remotes or running auditd in container you want to set it to **false**                                                                                  |
| `auditd_flush`                   |          incremental_async           | How autid handles flushing audit logs to disk. Possible values: none, incremental, incremental_async, data, sync                                                                                                           |
| `auditd_freq`                    |                  10                  | How many records will be written until explicit flush to disk is issued                                                                                                                                                    |
| `auditd_log_file`                |       /var/log/audit/audit.log       | Auditd's log file directory                                                                                                                                                                                                |
| `auditd_write_logs`              |                 yes                  | You want to write logs to disk, do not change!                                                                                                                                                                             |
| `auditd_log_format`              |               ENRICHED               | Auditd's log format either use ENRICHED or RAW ENRICHED will resolve all uid, gid, syscall, architecture, and socket address information before writing the event to disk                                                  |
| `auditd_log_group`               |                 root                 | Loggroup which auditd uses to set the group of logfiles Can be UID or spelled out                                                                                                                                          |
| `auditd_num_logs`                |                  5                   | Keep number of rotated logfiles before delteting oldest                                                                                                                                                                    |
| `auditd_max_log_file`            |                 100                  | Max log file size in MB                                                                                                                                                                                                    |
| `auditd_space_left`              |                 1024                 | Min space left on disk in MB                                                                                                                                                                                               |
| `auditd_admin_space_left`        |                  0                   | Same as auditd_space_left except it hast to be slightly less than auditd_space_left (more critical) If value is greater, same or 0 it will be set to auditd_space_left - 1                                                 |
| `auditd_space_left_action`       |                syslog                | Action to be taken when auditd_space_lest will be fallen below it's value                                                                                                                                                  |
| `auditd_admin_space_left_action` |               suspend                | Same as auditd_space_left_action                                                                                                                                                                                           |
| `auditd_disk_full_action`        | {{ auditd_admin_space_left_action }} | Action to be taken if disk is detected as full. If empty it will be the same as auditd_admin_space_left_action                                                                                                             |
| `auditd_disk_error_action`       | {{ auditd_admin_space_left_action }} | Actions to be taken if auditd has encountered errors while writting logs to disk. If empty it will be the same as auditd_admin_space_left_action.                                                                          |
| `auditd_extra_config`            |                  {}                  | Pass extra configuration parameters for auditd.conf                                                                                                                                                                        |
| `auditd_rules_buffer_size`       |                 8096                 | Buffer size                                                                                                                                                                                                                |
| `auditd_rules_backlog_wait_time` |                60000                 | backlog time                                                                                                                                                                                                               |
| `auditd_rules_failure_mode`      |                  1                   | Handles what will happen if auditd detects failuers in cs_configuration Possible values: 0, 1, 2. 0 - Nothing happens, 1 - Log to syslog, 2 - Kernel panic                                                                 |
| `auditd_rules_set_immutable`     |                false                 | Flag to enable immutability of auditd rules. If enabled, you have to reboot if there are rule changes                                                                                                                      |
| `auditd_rules_default`           |                 true                 | Enables default rule set. See templates/default.rules.j2                                                                                                                                                                   |
| `auditd_rules_extra_rules_set`   |                  []                  | Define either extra rules or a complete new rule set, each list field will be a new rule. If auditd_rules_default = false, only extra rules will be used, else auditd_rules_default = true, rules will be added to the end |
| `auditd_src_rules_directory`     |                  ""                  | Local source directory, where custom rules are stored to copy to target, if this variable is used, no rule set from above will be used. You have to create all your rules by yourself                                      |

## Handlers
| Name             | Description                           |
| ---------------- | ------------------------------------- |
| `restart auditd` | uses service to restart auditd daemon |
| `reload auditd`  | uses service to reload audit daemon   |
| `load auditd`    | not implemented yes                   |

## Tags
| Name               | Description                                                        |
| ------------------ | ------------------------------------------------------------------ |
| `auditd_install`   | Installs auditd and checks if config and logging directory exists. |
| `auditd_configure` | Configures auditd's daemon and restarts it.                        |

## Dependencies
---------------

**None**

## Example Playbook
-------------------

```yaml
- name: Converge
  hosts: all
  debugger: on_failed
  roles:
    - role: ansible-role-auditd
  vars:
    auditd_local_events: "no"  # Do not record local events in docker container
    auditd_rules_extra_rules_set:
      - "-w /etc/vimrc -p rw -k user_prg"
    auditd_rules_set_immutable: true
```

## License

MIT License

## Contributors

Daniel von Eßen
