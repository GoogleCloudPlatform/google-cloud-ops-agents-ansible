Ansible Role for Cloud Ops
============================

This Ansible role installs the Cloud Ops monitoring agent (which is based on
`collectd`).

Install this directory in your roles path (usually in a `roles` directory
alongside your playbook) under the name `cloud_ops`:

```
git clone <this-git-repo> roles/cloud_ops
```

Requirements
------------

Permissions to Google Cloud API. If running on an old Compute Engine instance or
Compute Engine instances created without the default credentials, then you must
complete the following steps
https://cloud.google.com/monitoring/agent/authorization#before_you_begin

Role Variables
--------------

By default, no plugins are enabled, in which case the agent will only monitor
certain system resources. (Moreover, all of the following variables are ignored
when deploying to Windows.)

Each plugin is enabled using a variable like `<plugin>_enabled` --
e.g., `apache_enabled` -- where the default value is `no`.

For many of the plugins, simply enabling the plugin may suffice in simple cases.
In particular, please note that specifying a host to monitor other than
`localhost` is atypical, as the Cloud Ops monitoring service assumes that the
data sent by the agent pertains to the host on which the agent is running.

The following plugins are currently supported:
- Apache
- JVM Monitoring
- Memcached
- Mysql
- Nginx
- Redis
- StatsD

For more information please see [Monitoring third-party applications](https://cloud.google.com/monitoring/agent/plugins).

### Other variables

Setting the following variable to `yes` will cause existing configuration files
for disabled plugins to be removed. In that case, the role will not be additive,
and applying it more than once to the same host (perhaps because the host is in
multiple groups) is likely to yield surprising results.

```
remove_unmanaged: no
```

Example Playbook
----------------

```
# Example
- hosts: all
  become: yes
  roles:
    - role: cloud_ops
      vars:
        agent_type: "monitoring"
        apache_enabled: yes
```

License
-------

```
Copyright 2020 Google Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License");
you may not use this file except in compliance with the License.
You may obtain a copy of the License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software
distributed under the License is distributed on an "AS IS" BASIS,
WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
See the License for the specific language governing permissions and
limitations under the License.
```
