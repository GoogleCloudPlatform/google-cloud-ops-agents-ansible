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

The `agent_type` is a required variable used to specify which agent is being
configured. The available options are `monitoring`, `logging` and `ops_agent`.

The `package_state` variable can be used to specify the desired state of the
agent. The allowed values are `present` (default) and `absent`.

The `version` variable can be used to specify which version of the agent to
install. The allowed values are `latest` (default), `MAJOR_VERSION.*.*` and
`MAJOR_VERSION.MINOR_VERSION.PATCH_VERSION`.

The `main_config_file` variable can be used to supply a path to a custom
configuration file. This file will overwrite the configuration file on the
target VM.

For more information please see [Configuring the Cloud Monitoring agent](https://cloud.google.com/monitoring/agent/configuration).

By default, the agent only monitors and logs system resources like cpu, memory,
disk etc. The `additional_config_dir` variable can be used to enable third party
application monitoring and logging. All `.conf` files under this directory will be
deployed to the agent's plugin directory on the target VM. The main config file
should have a line that includes this directory.

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
        package_state: present
        version: latest
        config_file_local: collectd.conf
        additional_config_dir: plugins/

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
