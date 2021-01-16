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

The monitoring agent can be configured by supplying a path to a custom
configuration file using the variable `monitoring_main_config_file`. On the
target VMs, this custom configuration file will be deployed to the location at
`/etc/stackdriver/collectd.conf`.

For more information please see [Configuring the Cloud Monitoring agent](https://cloud.google.com/monitoring/agent/configuration).

By default, the agent only monitors system resources like cpu, memory, disk etc.
Third party application monitoring can be configured by supplying a path to a
directory containing plugin configuration files using the variable
`monitoring_additional_config_dir`. On the target VMs, all `.conf` files under
this directory will be deployed to the location at
`/etc/stackdriver/collectd.d`. The root level config file at
`/etc/stackdriver/collectd.conf` should have a line that includes this
directory.

For more information please see [Monitoring third-party applications](https://cloud.google.com/monitoring/agent/plugins).

Example Playbook
----------------

```
# Example
- hosts: all
  become: yes
  roles:
    - role: cloud_ops
      vars:
        agent_type: monitoring
        monitoring_config_file_local: collectd.conf
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
