Ansible Role: oracle-asm-metadata
=================================

This role performs ASM metadata backup including OCR/OLR ocrconfig manualbackup and export OCR/OLR contents and ASM meetadata to a file.

Backups location is describe by variable `oracle_asm_metadata_backup_dir` and logs by `oracle_asm_metadata_log_dir`.

Sample files, which are created during role execution:

    $ ls -latr /u01/app/psu/backup
    -rw------- 1 oracle dba 75081 Dec 16 14:29 OLR_export.2018-12-16_142956.dmp
    -rw-r--r-- 1 oracle dba 27211 Dec 16 14:30 asm_metadata.2018-12-16_142956.dmp

    $ ls -latr /u01/app/psu/log
    -rw-r--r-- 1 oracle  dba        1416 Dec 16 14:29 crsctl_stat_res.2018-12-16_142956.log
    -rw-r--r-- 1 oracle  dba         588 Dec 16 14:29 processes.2018-12-16_142956.log
    -rw-r--r-- 1 oracle  dba         515 Dec 16 14:29 OLR_check.2018-12-16_142956.log
    -rw-r--r-- 1 oracle  dba         655 Dec 16 14:29 OLR_backups.2018-12-16_142956.log


Supported OS:
-------------
* RedHat
* CentOS
* OracleLinux


Requirements
------------

This role uses `oracle` role.

Additionally, this role executes `sudo /path/to/grid/bin/*` commands without ansible privilege escalation feature.
Therefore, the user, who runs the role, must have `(root) NOPASSWD: /path/to/grid/bin/*` sudoers privilege configured.


Role Variables
--------------

Available variables are listed below, along with default values (see `defaults/main.yml`):

    # ASM metadata backup dir
    oracle_asm_metadata_backup_dir: '{{ oracle_default_backup_dir|default( "/u01/app/psu/backup", true ) }}'
    
    # ASM metadata log dir
    oracle_asm_metadata_log_dir: '{{ oracle_default_log_dir|default( "/u01/app/psu/log", true ) }}'
    
    # checks if DB is RAC or non-RAC
    oracle_asm_metadata_local_config: "{% if oracle_gi_info.rac_nodes|length == 0 %}-local{% else %}{% endif %}"
    
    # print collected information about Grid Infrastructure
    oracle_asm_metadata_print_gi_info: false



Dependencies
------------

This role uses `oracle` role.

Example Playbook
----------------

    - name: Backup ASM metadata
      hosts: ora-servers
      gather_facts: true
      become: true
      become_user: '{{ oracle_user }}'
    
      tasks:
    
      - import_role:
          name: oracle-gatherinfo-gi
    
      - include_role:
          name: oracle-asm-metadata
    
      - include_tasks: import_oracle-asm-metadata_with_delegate.yml
        with_items:
          - '{{ inventory_hostname }}'
          - '{{ oracle_gi_info.rac_remote_nodes }}'
        loop_control:
          label: "[host: {{ _oracle_backup_asm_metadata_host_outer_item }}]"
          loop_var: _oracle_backup_asm_metadata_host_outer_item


Inside `vars/main.yml` or `group_vars/..` or `host_vars/..`:


    #-----------------------------------------------
    # overrides role 'oracle-asm-metadata' variables
    #-----------------------------------------------

    # ASM metadata backup dir
    oracle_asm_metadata_backup_dir: /other/path/backup

    # ASM metadata log dir
    oracle_asm_metadata_log_dir: /other/path/log


License
-------

GPLv3 - GNU General Public License v3.0

Author Information
------------------

This role was created in 2018 by [Krzysztof Lewandowski](mailto:Krzysztof.Lewandowski@fastmail.fm).


