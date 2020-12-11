ansible-role-aspera-server
==============================================

Ansible role to install and configure IBM Aspera HSTS Server.
This role also configures exactly `1` Transfer User and `1` Node API user.


Role Variables
----------------------------------------------

Following installation level variables are defined:

```text
# RPM packages required for configuration of IBM Aspera HSTS Server and "Transfer/Node API" users.
#   Required: No
#   Comment:  Very unlikely to supply this variable, leave it as is.
aspera_prereq_packages:
  - httpd-tools
  - perl-Data-Dumper
  - perl-Digest-MD5
  - perl-Digest-SHA
  - perl-Digest-SHA1

# IBM Aspera HSTS Server "RPM" package
#   Required: Yes
#   Comment:  You are required to provide this variable as:
#     1. Direct download path: http://download.asperasoft.com/sw/ondemand/3.9.7/ibm-aspera-hsts-3.9.6.178540-linux-64.rpm
#     2. Pre-download the artifact to the target server and provide path reference, e.g. /tmp/ibm-aspera-hsts-3.9.6.178540-linux-64.rpm
aspera_package: ~

# IBM Aspera HSTS Server License file
#   Required: Yes
#   Comment:  Provide full text of the license as:
#     1. aspera_license_text: |
#          ENCRYPTED_LICENSE_TEXT_BEGIN...
#          ...
#          ENCRYPTED_LCENSE_TEXT_END
#     2. aspera_license_text: "{{ lookup('file', '/full_path_to_aspera-license_file' ) }}"
aspera_license_text: ~
```

Following Transfer User variables are defined:

```text
# Transfer User Group
#   Required: No
#   Comment:  If not supplied, `aspxfergroup` Transfer User Group will be created.
aspera_transfer_user_group: aspxfergroup

# Transfer User
#   Required: No
#   Comment:  If not supplied, `xferuser` Transfer User will be created.
aspera_transfer_user_name: xferuser

# Transfer User Password
#   Required: No
#   Comment:  If not supplied, `xfer123` Transfer User Password will be used
aspera_transfer_user_pwd: xfer123
```

Following Node API User variables are defined:

```text
# Node API User
#   Required: No
#   Comment:  If not supplied, `nodeuser` Node API User will be created.
aspera_node_api_user_name: nodeuser

# Node API User Password
#   Required: No
#   Comment:  If not supplied, `node123` Node API User Password will be used.
aspera_node_api_user_pwd: node123
```

Other variables:

```text
# Global Data Dir
#   Required: No
#   Comment:  When supplied, point to a directory that is empty. Access to this directory is denied.
aspera_global_data_dir: /data/__global__

# Transfer User Directory
#   Required: No
#   Comment:  If not supplied, `/data/shared` directory will be used as absolute path for transfer files.
aspera_data_dir: /data/shared

# Server Name
#   Required: YES
#   Comment:  Provide a server name that is either a FQDN or an IP address
#             which is accessible from the Transfer Client.
#   Note:     The default value only works in very limited use case.
aspera_server_name: "{{ inventory_hostname }}"

# HTTP Fallback Port
#   Required: No
#   Comment:  This port will be used by Node API to fall back to http protocol.
aspera_http_port: 9091
```

Example
----------------------------------------------

This is an example of a minimum configuration variables:

```text
---

# - Variables -
aspera_package: http://download.asperasoft.com/sw/ondemand/3.9.7/ibm-aspera-hsts-3.9.6.178540-linux-64.rpm
aspera_license_text: |
  PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4NCjxsaWNl
  bnNlIHZlcnNpb249IjEiPg0KICA8IS0tIFZvaWQgaWYgbW9kaWZpZWQgLS0+
  ...
  ...
  SitFL29RVUovdkNEY2VsMTNXMXlhZllieC9IU2ZMbHZDdEcNCkRzZW5lOWpW
  cGU4QmZpVkhQU1ExYWdaQkc0NmlEcEM2U2NOY1hjYmxkZz09DQo=
aspera_server_name: my-aspera.subdomain.domain.root

# - Install -
# Run from a console
:% ansible-playbook --inventory ${inventory_file} --extra-vars @ ${extra_vars_file} ${playbook_file}

# - Test -
# Run from a console
:% curl -i -XGET -u nodeuser:node123 http://${aspera_server_name}:9091/
```

Comprehensive example:

```text
# - Variables -
aspera_package: http://download.asperasoft.com/sw/ondemand/3.9.7/ibm-aspera-hsts-3.9.6.178540-linux-64.rpm
aspera_license_text: |
  PD94bWwgdmVyc2lvbj0iMS4wIiBlbmNvZGluZz0iVVRGLTgiPz4NCjxsaWNl
  bnNlIHZlcnNpb249IjEiPg0KICA8IS0tIFZvaWQgaWYgbW9kaWZpZWQgLS0+
  ...
  ...
  SitFL29RVUovdkNEY2VsMTNXMXlhZllieC9IU2ZMbHZDdEcNCkRzZW5lOWpW
  cGU4QmZpVkhQU1ExYWdaQkc0NmlEcEM2U2NOY1hjYmxkZz09DQo=
aspera_server_name: my-aspera-prod.subdomain.domain.root

aspera_transfer_user_group: aspera_corporate_usergroup
aspera_transfer_user_name: aspera_corporate_user
aspera_transfer_user_pwd: aspera_corporate_user_complex_password

aspera_node_api_user_name: aspera_node_user
aspera_node_api_user_pwd: aspera_node_user_complex_password

aspera_global_data_dir: /data/__global__
aspera_data_dir: /nas_mounted_volume/shared_path
aspera_http_port: 8891

# - Install -
# Run from a console
:% ansible-playbook --inventory ${inventory_file} --extra-vars @ ${extra_vars_file} ${playbook_file}

# - Test -
# Run from a console
:% curl -i -XGET -u ${aspera_node_api_user_name}:${aspera_node_api_user_pwd} http://${aspera_server_name}:${aspera_http_port}/
```

If you want to test your install, visit 
https://github.com/rjaryal/aspera-connect-client
