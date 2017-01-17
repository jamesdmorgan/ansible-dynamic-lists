# Dynamic Ansible lists

### Problem

* ec2_groups expects rules to be passed in one go. You can't iterate over multiple lists
* You can't dynamically build lists using loops

### Approach

The rules list can be built dynamically in a jinja2 template. This can then be looked up and read in
a variable using **lookup**  and the **from_yaml** filter

```
{{ lookup('template', './security_groups.j2') | from_yaml }}
```

### Output

Using this approach we can key into our dict and build up a dynamic list. This removes duplication and is pretty flexible.
The downside is that configuration is in a template instead of group_vars

```
$ ansible-playbook test.yml  -c local
 [WARNING]: Host file not found: /etc/ansible/hosts

 [WARNING]: provided hosts list is empty, only localhost is available


PLAY [localhost] ***************************************************************

TASK [setup] *******************************************************************
ok: [localhost]

TASK [debug] *******************************************************************
ok: [localhost] => (item=test) => {
    "item": "test",
    "msg": [
        {
            "from_port": 80,
            "group_name": "test_riemann_grafana_elb",
            "proto": "tcp",
            "to_port": 80
        },
        {
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5556
        },
        {
            "cidr_ip": "10.XXX.0.0/16",
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.0.0/16",
            "from_port": 5555,
            "proto": "udp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.128.0/20",
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.128.0/20",
            "from_port": 5555,
            "proto": "udp",
            "to_port": 5555
        }
    ]
}
ok: [localhost] => (item=uat) => {
    "item": "uat",
    "msg": [
        {
            "from_port": 80,
            "group_name": "uat_riemann_grafana_elb",
            "proto": "tcp",
            "to_port": 80
        },
        {
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5556
        },
        {
            "cidr_ip": "10.XXX.0.0/16",
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.0.0/16",
            "from_port": 5555,
            "proto": "udp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.128.0/20",
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.128.0/20",
            "from_port": 5555,
            "proto": "udp",
            "to_port": 5555
        }
    ]
}
ok: [localhost] => (item=prod) => {
    "item": "prod",
    "msg": [
        {
            "from_port": 80,
            "group_name": "prod_riemann_grafana_elb",
            "proto": "tcp",
            "to_port": 80
        },
        {
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5556
        },
        {
            "cidr_ip": "10.XXX.128.0/16",
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.128.0/16",
            "from_port": 5555,
            "proto": "udp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.128.0/16",
            "from_port": 5555,
            "proto": "tcp",
            "to_port": 5555
        },
        {
            "cidr_ip": "10.XXX.128.0/16",
            "from_port": 5555,
            "proto": "udp",
            "to_port": 5555
        }
    ]
}

PLAY RECAP *********************************************************************
localhost                  : ok=2    changed=0    unreachable=0    failed=0
```
