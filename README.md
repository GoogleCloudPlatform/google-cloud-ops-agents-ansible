Ansible Role for Cloud Ops
==========================

This Ansible role installs the Cloud Ops agents.

Install this directory in your roles path (usually in a `roles` directory
alongside your playbook) under the name `google_cloud_ops_agents`:

``` git clone <this-git-repo> roles/google_cloud_ops_agents ```

Requirements
------------

Permissions to the Google Cloud API. If you are running an old Compute Engine
instance or Compute Engine instances created without the default credentials,
then you must complete the following steps
https://cloud.google.com/monitoring/agent/authorization#before_you_begin.

Role Variables
--------------

The `agent_type` is a required variable used to specify which agent is being
configured. The available options are `monitoring`, `logging` and `ops-agent`.

The `package_state` variable can be used to specify the desired state of the
agent. The allowed values are `present` (default) and `absent`.

The `version` variable can be used to specify which version of the agent to
install. The allowed values are `latest` (default), `MAJOR_VERSION.*.*` and
`MAJOR_VERSION.MINOR_VERSION.PATCH_VERSION`, which are described in detail
below.

`version=latest` This setting makes it easier to keep the agent version up to
date, however it does come with a potential risk. When a new major version is
released, the policy may install the latest version of the agent from the new
major release, which may introduce breaking changes. For production
environments, consider using the `version=MAJOR_VERSION.*.*` setting below for
safer agent deployments.

`version=MAJOR_VERSION.*.*` When a new major release is out, this setting
ensures that only the latest version from the specified major version is
installed, which avoids accidentally introducing breaking changes. This is
recommended for production environments to ensure safer agent deployments.

`version=MAJOR_VERSION.MINOR_VERSION.PATCH_VERSION` This setting is not
recommended since it prevents upgrades of new versions of the agent that include
bug fixes and other improvements.

The `main_config_file` variable can be used to supply an absolute or relative
path to a custom configuration file. This file will overwrite the configuration
file on the target VM.

For more information, please see [Configuring the Monitoring
Agent](https://cloud.google.com/monitoring/agent/configuration), [Configuring
the Logging
Agent](https://cloud.google.com/logging/docs/agent/configuration?hl=en#configure)
or [Configuring the Ops
Agent](https://cloud.google.com/stackdriver/docs/solutions/ops-agent/configuration?hl=en).

By default, the agent only monitors and logs system resources like cpu, memory,
disk etc. Third party application monitoring and logging can be configured by
supplying a path to a directory containing plugin configuration files using the
variable `additional_config_dir`. All `.conf` files under this directory will be
deployed to the agent's plugin directory on the target VM. The main config file
should have a line that includes this directory. Please note that this variable
can only be specified when configuring the monitoring or logging agents.

For more information, please see [Monitoring third-party
applications](https://cloud.google.com/monitoring/agent/plugins).

Example Playbook
----------------

```
# Example
- hosts: all
  become: true
  roles:
    - role: google_cloud_ops_agents
      vars:
        agent_type: monitoring
        package_state: present
        version: latest
        main_config_file: collectd.conf
        additional_config_dir: plugins/

    - role: google_cloud_ops_agents
      vars:
        agent_type: logging
        package_state: present
        version: 1.*.*
```

License
-------

```
Copyright 2020 Google Inc. All rights reserved.

Licensed under the Apache License, Version 2.0 (the "License"); you may not use
this file except in compliance with the License.  You may obtain a copy of the
License at

    http://www.apache.org/licenses/LICENSE-2.0

Unless required by applicable law or agreed to in writing, software distributed
under the License is distributed on an "AS IS" BASIS, WITHOUT WARRANTIES OR
CONDITIONS OF ANY KIND, either express or implied.  See the License for the
specific language governing permissions and limitations under the License.
```
