# nickdtodd.system_limits

A role to set system limits for users/groups across sysctl and limit interfaces. Ensures pam includes module is loaded where appropriate.

Role Variables with their default settings
--------------
    # Limits variables
    # Processed in order so the naming is important

    limits_file_name: limits.conf # the name of the file that will be created in /etc/security/limits.d/
    limits_user: root # the owner of that file
    limits_group: root # the group of that file
    limits_chmod: "0644" # the permissions of that file
    limits_run_task: True
    # See below for example
    limits_domains: []

    # Sysctl variables

    sysctl_file_name: sysctl.conf # The file that will be created in /etc/sysctl.d/ (Must end with .conf)
    sysctl_user: roota # The owner of that file
    sysctl_group: root # The group of that file
    sysctl_chmod: "0644" # The permissions of that file
    sysctl_run_task: True # Whether the task should be run
    sysctl_reload: True # Whether sysctl should be reloaded (sysctl -p)
    # See below for example
    sysctl_values: []

    # PAM variables
    pam_run_task: True # Whether the task should be run
    # See example below
    pam_files_to_check: []

Example Playbook
-------------------------
    - hosts: all
      sudo: true
      vars:
        limits_domains:
          - domain: root
            type: soft
            item: nofile
            value: 10000
          - domain: '@staff'
            type: '-'
            item: nofile
            value: 2048
        sysctl_values:
          - variable: fs.file-max
            value: 2048
        pam_files_to_check:
          - cron
          - sudo
      roles:
        - { role: nickdtodd.system_limits }

What's going on here? First, the number of maximum open files limits are being set for the user `root` and group `staff`. These limits are put in their own file in `/etc/security/limits.d/` and can be checked with `ulimit -a`.

Next, A file is created in `/etc/sysctl.d/` to set the `fs.file-max` to `2048`.

Finally, we are ensuring that line `session required pam_limits.so` is present in `/etc/pam.d/cron` and `/etc/pam.d/sudo`.

License
-------

BSD

Author Information
------------------

Nick Todd [github](https://github.com/nickdtodd)
