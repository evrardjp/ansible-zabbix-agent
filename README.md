Ansible-Zabbix-Agent
====================

This simple role is used for deploying a zabbix agent on your hosts.
It will by default, install the zabbix-agent packages and edit the
configuration file to speak to your zabbix-server/proxy, based on
the role variables.

This role can also be used to deploy executable check scripts
and run them with crontab.

Requirements
------------

The role requirements are what you're used to have for package management using ansible. So, nothing fancy.

Role Variables
--------------

*   zabbix_server_ip: List of comma delimited IP addresses (or hostnames) of Zabbix servers.
    Incoming connections will be accepted only from the hosts listed here.

    Defaults to 127.0.0.1
*   zabbix_server_serveractive_ip: List of comma delimited IP:port (or hostname:port) pairs of Zabbix servers for active checks.
    If port is not specified, default port is used. IPv6 addresses must be enclosed in square brackets if port for that host is specified.

    Not set if undefined.
*   zabbix_agent_hostname: Hostname (case sensitive) that will be used for the system in zabbix server.

    Defaults to ansible_hostname.
*   zabbix_agent_folder: This is the folder where zabbix-agent configuration file is located.

    Defaults to /etc/zabbix/
*   zabbix_agent_additional_configuration_folder: Folder to store additional zabbix variables.

    Defaults to "{{zabbix_agent_folder}}/zabbix_agentd.conf.d/".
*   zabbix_agent_listen_ip: The IP on which the zabbix-agent can be reached.
*   zabbix_agent_checks. This is an optional variable (dict) that helps the user upload specific checks, depending on its inventory.
*   zabbix_agent_additional_configuration_files: This is an optional variable that upload configuration files.

Here is an example of zabbix_agent_checks:

    zabbix_agent_checks:
      - check1:
          script_src: PATH TO THE SCRIPT ON YOUR DEPLOY NODE. No defaults. Mandatory.
          script_dst: PATH TO THE SCRIPT ON YOUR DEPLOYED NODE (probably in "{{zabbix_agent_folder}}/scripts"). No defaults. Mandatory.
          script_mode: 0700 (the chmod, defaults to 0750)
          owner: root (used for the chown and to know who will run the cron, defaults to zabbix)
          use_cron: True
          cron_minute: (defaults to '*')
          cron_hour: (defaults to '*')
          cron_day: (defaults to '*')
          cron_weekday: (defaults to '*')
          cron_month: (defaults to '*')
          cron_job: (This is useful if you want to pass arguments to the check script. Defaults to "{{script_dst}}")
          requirements:
            pkg:
              - pkg1
              - whois
            pip:

Here is an example of zabbix_agent_additional_configuration_files:

    zabbix_agent_additional_configuration_files:
      - file: AllowRoot.conf
        src_path: /etc/openstack_deploy/files/zabbix_agent_allowroot.conf
      - file: metadata-and-checks.conf
        src_path: /etc/openstack_deploy/files/zabbix_agent_metadata-and-checks.conf

Dependencies
------------

None that I'm aware of.

Example Playbook
----------------

    - hosts: all
      roles:
         - { role: evrardjp.zabbix_agent, zabbix_server_ip: 192.168.0.1 }

License
-------

Apache2

Author Information
------------------

Jean-Philippe Evrard <jean-philippe@evrard.me>
