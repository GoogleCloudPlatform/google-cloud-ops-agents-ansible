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

A specific version of the agent can be installed by modifying the `version`
variable. The allowed values are `latest-major` (default), `latest`,
`MAJOR_VERSION.*.*` and `MAJOR_VERSION.MINOR_VERSION.PATCH_VERSION`.

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
        version: latest
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
