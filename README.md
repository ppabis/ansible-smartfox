SmartfoxServer 2X Ansible Role
=========
Unofficial Ansible role that installs and configures specific version of
SmartfoxServer 2X on Linux. Can also upgrade the version to some extent (if
there are no breaking changes for config). Learn more about [SmartFox here](https://smartfoxserver.com). Also remember to read the
[license and use policy](https://www.smartfoxserver.com/support/faq#BG01) of SmartFox on the official website.

Platform Support
----------------
Designed and tested on:
- Ubuntu 20.04
- Debian 10
- Rocky Linux 8
- AlmaLinux 8
- x86_64 and ARM64 architectures

Requirements
------------
- `become: yes` (to root)
- `community.general` collection (for `xml` module)
- `community.crypto` collection (for `openssl_privatekey` module)
- enough memory/swap if `lxml` is not present on the target machine (for example
  on Rocky Linux 8, it needs more than 2GB to build `lxml`)

Role Variables
--------------
All of the variables are optional but it's highly recommended to set most of
them to understand what's going on.

### Basic installation essentials

- `smartfox_version` - default `2.18.0` - used to download the correct version
  and verify currently installed one
- `smartfox_download_url` - default
  `https://smartfoxserver.com/downloads/sfs2x/SFS2X_unix_{{ smartfox_version | replace('.', '_') }}.tar.gz` -
  useful to set if you have some custom pre-built package
- `smartfox_target_directory` - default `"/opt/smartfox"` - where to extract the
  archive with SmartfoxServer
- `sfs2x_directory` - more of a helper variable, default
  `"{{ smartfox_target_directory }}/SmartFoxServer_2X/SFS2X"` - change in case
  your archive contains SmartFox in a different place
- `smartfox_user` - default `smartfox` - user to create and run SmartfoxServer

### Configuration
- `smartfox_ssl_keystore_file` - default `mykeystore.jks` copied from the role.
  **You should change this and provide a file!**
- `smartfox_ssl_keystore_password` - default `VeRySeCrEtPa5s` - password for the JKS.
  **You should change this too!**
- `smartfox_admin_user` - default `Gamemaster` - initial admin username.
- `smartfox_admin_password` - default `G4M3mast3r` - initial admin password.
  **Use your own.**
- `smartfox_admin_allowed_ips` - default `["0.0.0.0"]` - if you don't set it
  yourself, you will be locked out of admin panel. Ideally use your own public
  IP or `127.0.0.1` and reverse proxy to it. If you put an empty array here, it
  would be accessible from anywhere.
- `smartfox_lock_uploads` - default `true` - whether to lock extensions and
  servlets uploads to the server using admin panel

- `smartfox_tcp_port` - default `9933` - TCP port to listen on
- `smartfox_udp_port` - default = TCP - UDP port to listen on
- `smartfox_banned_ips` - default `[]` - list of IPs to block from connecting
  to the server (blacklist)
- `smartfox_max_connections_per_address` - max amount of connections from one
  IP address. Default `999999`

Dependencies
------------
No role dependencies except requirements.

Example Playbook
----------------

```yaml
- hosts: gameserver
  become: yes

  vars_files:
    - vars/keystore-password.yml # encrypted with ansible-vault

  vars:
    smartfox_admin_user: admin
    smartfox_admin_password: admin
    smartfox_admin_allowed_ips:
      - "88.99.77.66"
    smartfox_ssl_keystore_file: keystore.jks
  
  roles:
    - role: ppabis.smartfox
```

Also `files/` directory with your own `extensions/`, `zones/` and `keystore.jks`.

License
-------
MIT - the playbook/role
The SmartFox license is available on the official website.

Author Information
------------------
Based on my blog tutorials on how to install SmartfoxServer 2X using Ansible.
The posts are available here:

- https://pabis.eu/blog/2023-01-02-Deploy-Smartfox-Server-with-Ansible.html
- https://pabis.eu/blog/2023-01-20-Deploy-Smartfox-Server-with-Ansible-2.html
- https://pabis.eu/blog/2023-02-01-Deploy-Smartfox-With-Ansible-3.html
- https://pabis.eu/blog/2023-02-21-Deploy-Smartfox-Server-with-Ansible-4.html
