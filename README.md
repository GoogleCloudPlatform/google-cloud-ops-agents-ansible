Ansible Role for Stackdriver
============================

This Ansible role installs the Stackdriver monitoring agent (which is based on
`collectd`) and configures its plugins for monitoring third-party applications.

Install this directory in your roles path (usually in a `roles` directory
alongside your playbook) under the name `stackdriver`:

```
git clone this-git-repo roles/stackdriver
```

Requirements
------------

The Google Cloud monitoring API must be enabled.

Role Variables
--------------

By default, no plugins are enabled, in which case the agent will only monitor
certain system resources. (Moreover, all of the following variables are ignored
when deploying to Windows.)

Each plugin is enabled using a variable like `stackdriver_PLUGIN_enabled` --
e.g., `stackdriver_apache_enabled` -- where the default value is `no`.

For many of the plugins, simply enabling the plugin may suffice in simple cases.
In particular, please note that specifying a host to monitor other than
`localhost` is atypical, as the Stackdriver service assumes that the data sent
by the agent pertains to the host on which the agent is running.

The YAML snippets below are excerpted from `defaults/main.yml` to summarize
the variables and default values available for you to override.

### apache

The default URL uses `local-stackdriver-agent.stackdriver.com`, which
resolves to `127.0.0.1`, as an alias for `localhost`, so that the web
server status page can be served from a named virtual host defined for
this purpose. See
[Stackdriver's instructions](http://support.stackdriver.com/customer/portal/articles/1491752-apache-plugin)
regarding how to configure Apache accordingly.

```
stackdriver_apache_enabled: no
stackdriver_apache_status_url: http://local-stackdriver-agent.stackdriver.com/server-status?auto

stackdriver_apache_username: ""
stackdriver_apache_password: ""
```

### elasticsearch

The default URL is appropriate for elasticsearch version 1.0 and later. For
earlier versions, use `http://localhost:9200/_cluster/nodes/_local/stats?all=true`.

```
stackdriver_elasticsearch_enabled: no
stackdriver_elasticsearch_url: http://localhost:9200/_nodes/_local/stats/
```
### jvm

```
stackdriver_jvm_enabled: no
stackdriver_jvm_host: localhost
stackdriver_jvm_port: 5000
```

### kafka

```
stackdriver_kafka_enabled: no
stackdriver_kafka_host: localhost
stackdriver_kafka_port: 9092
```

### memcached

```
stackdriver_memcached_enabled: no

stackdriver_memcached_host: localhost
stackdriver_memcached_port: 11211
```

### mongodb

```
stackdriver_mongodb_enabled: no

stackdriver_mongodb_host: localhost
stackdriver_mongodb_port: 27017

stackdriver_mongodb_username: ""
stackdriver_mongodb_password: ""

stackdriver_mongodb_prefer_secondary_query: false
```

### mysql

When connecting to the MySQL server, the host name `localhost` means to
use a Unix domain socket. The default socket file will be used if none is
specified. To connect via TCP/IP, specify a host name of `127.0.0.1`. The
default port is 3306.

You need to provide the user name and password of the user who will run
the `SHOW STATUS` command.

The mysql plugin requires the `mysql-client` package.

```
stackdriver_mysql_enabled: no

stackdriver_mysql_host: localhost
#stackdriver_mysql_port: 3306
#stackdriver_mysql_socket: "..."

stackdriver_mysql_username: root
stackdriver_mysql_password: ""
```

### nginx

Like for Apache, the default URL for nginx uses
`local-stackdriver-agent.stackdriver.com` as an alias for `localhost`. See
[Stackdriver's instructions](http://support.stackdriver.com/customer/portal/articles/1491751-nginx-plugin)
for how to configure nginx accordingly.

```
stackdriver_nginx_enabled: no
stackdriver_nginx_status_url: http://local-stackdriver-agent.stackdriver.com/nginx_status

stackdriver_nginx_username: ""
stackdriver_nginx_password: ""
```

### postgresql

When connceting to PostgreSQL the plugin will connect using a local Unix
socket. To connect via TCP/IP, specify a host name of `127.0.0.1`. The
default port is 5432. If a port is provided without a host name, the
pluggin will use Unix domain socket to connect to the database. The port 
is incorporated into the path: `/var/run/postgresql/.s.PGSQL.5432`.

You need to provide the user name and password of the user who will run
the `\dt` command for each database specified. By default the user is
`stackdriver-agent` with a blank password. If the defaults are sufficient
for you then make sure the user can connect by adding the following to your
`pg_hba.conf` file:

```
local   all     stackdriver-agent   md5
```

or for TCP connections:

```
host    all     stackdriver-agent  127.0.0.1/32     md5
```

The PostgreSQL plugin requires the `libpg` package.

```
stackdriver_postgresql_enabled: no

stackdriver_postgresql_databases: []
# stackdriver_postgresql_host:
stackdriver_postgresql_port: 5432
stackdriver_postgresql_user: stackdriver-agent
stackdriver_postgresql_password: ""
```

### rabbitmq

To configure monitoring of RabbitMQ, you must provide a list of dictionaries
specifying the queues to be monitored. For each queue, you need only specify
the name:

```
# Example
stackdriver_rabbitmq_enabled: yes
stackdriver_rabbitmq_queues:
 - name: hello
```

```
stackdriver_rabbitmq_enabled: no
stackdriver_rabbitmq_queues: []

stackdriver_rabbitmq_username: guest
stackdriver_rabbitmq_password: guest

stackdriver_rabbitmq_host: localhost
stackdriver_rabbitmq_port: 15672    # Change this to 55672 for versions < 3.0.
stackdriver_rabbitmq_vhost: "%2F"   # "%2F" encodes "/", the default virtual host.
```

Be sure to also enable the [RabbitMQ management plugin](https://www.rabbitmq.com/management.html).

### redis

The redis plugin requires the hiredis library, which this role installs if the
plugin is enabled. On Red Hat and derivatives (CentOS, Amazon Linux...), the
library is installed from the EPEL repository, which must be configured, but
need not be enabled. It is configured and disabled by default on Amazon Linux.
On other Red Hat systems, you can install it with `yum install epel-release`.

```
stackdriver_redis_enabled: no

stackdriver_redis_host: localhost
stackdriver_redis_port: 6379
stackdriver_redis_password: ""

stackdriver_redis_timeout: 2000     # Seconds
```

### zookeeper

```
stackdriver_zookeeper_enabled: no
stackdriver_zookeeper_host: localhost
stackdriver_zookeeper_port: 2181
```

### Other variables

Setting the following variable to `yes` will cause existing configuration files
for disabled plugins to be removed. In that case, the role will not be additive,
and applying it more than once to the same host (perhaps because the host is in
multiple groups) is likely to yield surprising results.

```
stackdriver_remove_unmanaged: no
```

Setting the following variable to `yes` will cause the agent to log to
`/var/log/collectd.log` in addition to logging to syslog. This can be useful
for debugging.

```
stackdriver_logfile_enabled: no
```

Setting the following variable to `yes` will cause Ansible to send annotation
events reporting successful installation of the agent or updating of its
configuration on the monitoring dashboard. This feature currently does not
work on Google Cloud Platform.

```
stackdriver_events_enabled: no
```

Example Playbook
----------------

Here is a simple example of monitoring Apache:

```
# Example
- hosts: webservers
  become: yes
  become_method: sudo
  vars_files:
    - secrets.yml   # API key is defined here.
  roles:
    - role: stackdriver
      stackdriver_apache_enabled: yes
```

Here is an example of monitoring RabbitMQ:

```
# Example
- hosts: rabbits
  become: yes
  become_method: sudo
  vars_files:
    - secrets.yml   # API key is defined here.
  roles:
    - role: stackdriver
      stackdriver_rabbitmq_enabled: yes
      stackdriver_rabbitmq_username: "stackdriver"
      stackdriver_rabbitmq_password: "PASSWORD"
      stackdriver_rabbitmq_queues:
        - name: hello
```

License
-------

```
Copyright 2014 Google Inc. All rights reserved.

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
