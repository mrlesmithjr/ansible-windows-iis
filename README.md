<!-- START doctoc generated TOC please keep comment here to allow auto update -->
<!-- DON'T EDIT THIS SECTION, INSTEAD RE-RUN doctoc TO UPDATE -->
**Table of Contents**  *generated with [DocToc](https://github.com/thlorenz/doctoc)*

- [ansible-windows-iis](#ansible-windows-iis)
  - [Requirements](#requirements)
  - [Role Variables](#role-variables)
  - [Dependencies](#dependencies)
  - [Example Playbook](#example-playbook)
  - [License](#license)
  - [Author Information](#author-information)

<!-- END doctoc generated TOC please keep comment here to allow auto update -->

# ansible-windows-iis

An [Ansible](https://www.ansible.com) role to install [Windows - IIS](https://www.iis.net/)

## Requirements

## Role Variables

```yaml
---
# defaults file for ansible-windows-iis

windows_iis_role: []
  # - name: Web-Server
  #   include_management_tools: true
  #   include_sub_features: false
  #   state: absent
  #   subfeatures: []
  #     # - name: Web-Default-Doc
  #     #   state: present
  #     # - name: Web-Dir-Browsing
  #     #   state: present
  #     # - name: Web-Http-Errors
  #     #   state: present
  #     # - name: Web-Static-Content
  #     #   state: present
  #     # - name: Web-Http-Logging
  #     #   state: present
  #     # - name: Web-Stat-Compression
  #     #   state: present
  #     # - name: Web-Filtering
  #     #   state: present
  # - name: Web-Ftp-Server
  #   include_management_tools: true
  #   include_sub_features: false
  #   state: absent
  #   subfeatures: []

windows_iis_websites: []
  # - name: Default Web Site
  #   # ip: 192.168.250.10
  #   physical_path: c:\inetpub\wwwroot
  #   port: 80
  #   state: started
  #   upload_files: path/to/dir/

`upload_files` can be either a single file or a directory. If it is a directory
ending with a `/`, then the directory contents will be uploaded. If it is
a directory not ending with `/`, the directory itself will be uploaded under
`physical_path`.

```

## Dependencies

## Example Playbook

```yaml
---
- hosts: windows_iis_servers
  roles:
    - role: ansible-windows-iis
```

## Heartbeat monitor

The role comes bundled with a [meta/monitors.yml](meta/monitors.yml) template
that can be used by [Heartbeat](https://www.elastic.co/products/beats/heartbeat)
to check if the IIS websites are up and running. The template can be
configured via variables (they should be self-explanatory). To use it, you can
use some Ansible tasks to upload it to your Heartbeat instance. For example:

```yaml
- name: Add heartbeat host
  add_host:
    name: heartbeat_instance
    hostname: "{{ heartbeat.hostname }}"
    ansible_host: "{{ heartbeat.ansible_host }}"
    ansible_password: "{{ heartbeat.ansible_password }}"
    ansible_user: "{{ heartbeat.ansible_user }}"

- name: Upload role monitors
  template:
    src: "{{ item.1 + '/' + item.0 }}/meta/monitors.yml"
    dest: "/etc/heartbeat/monitors.d/{{ inventory_hostname }}.{{ item.0.split('.')[-1] }}.yml"
  when: (item.1 + '/' + item.0 + '/meta/monitors.yml') is file
  loop: "{{ roles | product(lookup('config', 'DEFAULT_ROLES_PATH')) | list }}"
  delegate_to: heartbeat_instance
```

## License

MIT

## Author Information

Larry Smith Jr.

-   [@mrlesmithjr](https://www.twitter.com/mrlesmithjr)
-   [EverythingShouldBeVirtual](http://everythingshouldbevirtual.com)
-   [mrlesmithjr.com](http://mrlesmithjr.com)
-   mrlesmithjr [at] gmail.com
