openvpn
=========

This role installs OpenVPN, configures it as a server, sets up networking (either iptables or firewalld), and can optionally create client certificates.

Tested OSes:
- Fedora 20/21
- CentOS 6
- Ubuntu trusty (14.04)
- RHEL7.2

Should be working OSes:
- All Fedora
- CentOS 6/7
- Ubuntu trusty & later


Requirements
------------

openvpn must be available as a package in yum/apt! For CentOS users, this means running `yum install epel-release` *prior* to running this playbook.

Ubuntu precise has a [weird bug](https://bugs.launchpad.net/ubuntu/+source/iptables-persistent/+bug/1002078) that might make the iptables-persistent install fail. There is a [workaround](https://forum.linode.com/viewtopic.php?p=58233#p58233).

Role Variables
--------------

TBD

Dependencies
------------

Does not depend on any other roles

Example Playbook
----------------

    - name: Deploy openvpn server
      hosts: vpnserver
      sudo: yes
      roles:
          - { role: cisco.resolv_conf, sudo: yes }
          - { role: martian.bootrhel, sudo: yes }
          - role: ansible-role-openvpn
            tls_auth: |
              thisiscool
            client:
              config_file: /etc/openvpn/client.ovpn
              passphrase: metacloud
              crt: client.pem
              key: client.key
              ssl_certs_inventory:
                # ca.key
                - name: ca
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----
                - name: client
                  combined: yes
                  key: |
                    -----BEGIN RSA PRIVATE KEY-----
                    -----END RSA PRIVATE KEY-----
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----
            server:
              ip: 172.16.10.101
              net: 10.8.0.0
              mask: 255.255.255.0
              duplicate_cn: yes
              config_file: /etc/openvpn/server.conf
              passphrase: metacloud
              crt: server.pem
              key: server.key
              dh:
                name: dh2048.pem
                content: |
                  -----BEGIN DH PARAMETERS-----
                  -----END DH PARAMETERS-----
              ssl_certs_inventory:
                # ca.key
                - name: ca
                  combined: yes
                  key: |
                    -----BEGIN RSA PRIVATE KEY-----
                    -----END RSA PRIVATE KEY-----
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----
                  crl: |
                    -----BEGIN X509 CRL-----
                    -----END X509 CRL-----
                - name: server
                  combined: yes
                  key: |
                    -----BEGIN RSA PRIVATE KEY-----
                    -----END RSA PRIVATE KEY-----
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----
    - name: Deploy openvpn client
      hosts: vpnclient
      sudo: yes
      roles:
          - { role: cisco.resolv_conf, sudo: yes }
          - { role: martian.bootrhel, sudo: yes }
          - role: ansible-role-openvpn
            client: yes
            tls_auth: |
              thisiscool
            client:
              config_file: /etc/openvpn/client.ovpn
              passphrase: metacloud
              crt: client.pem
              key: client.key
              ssl_certs_inventory:
                # ca.key
                - name: ca
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----
                - name: client
                  combined: yes
                  key: |
                    -----BEGIN RSA PRIVATE KEY-----
                    -----END RSA PRIVATE KEY-----
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----
            server:
              ip: 172.16.10.101
              net: 10.8.0.0
              mask: 255.255.255.0
              duplicate_cn: yes
              config_file: /etc/openvpn/server.conf
              passphrase: metacloud
              crt: server.pem
              key: server.key
              dh:
                name: dh2048.pem
                content: |
                  -----BEGIN DH PARAMETERS-----
                  -----END DH PARAMETERS-----
              ssl_certs_inventory:
                # ca.key
                - name: ca
                  combined: yes
                  key: |
                    -----BEGIN RSA PRIVATE KEY-----
                    -----END RSA PRIVATE KEY-----
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----
                  crl: |
                    -----BEGIN X509 CRL-----
                    -----END X509 CRL-----
                - name: server
                  combined: yes
                  key: |
                    -----BEGIN RSA PRIVATE KEY-----
                    -----END RSA PRIVATE KEY-----
                  cert: |
                    -----BEGIN CERTIFICATE-----
                    -----END CERTIFICATE-----


License
-------

GPLv2

Author Information
------------------

Written by Kyle Lexmond
Modified by David Lapsley
