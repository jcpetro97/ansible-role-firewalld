Ansible Firewalld Role
=========

Allows you to configure firewalld.

Config options:
* default zone
* interface of a zone
* source of a zone
* service rules
* port rules
* rich rules

Requirements
------------

Tested on Ubuntu 18.04 and 20.04

Role Variables
--------------
**It is not necessary to use all these variable blocks, you can use only the config options you really need.**


The following variable is used to define the default zone of firewalld:

```
    default_zone: (optional, default: public)
```

---

The following variables are used to define which interfaces assigned to zones:

```
    firewalld_zone_interfaces:
      - name: (required, e.g. public)
        interfaces: (required, list of interfaces, one or multiple possible)
    firewalld_zone_interfaces:          
      - interface: (required, e.g eth0)
        zone: (required, zone you are adding interface to)
        immediate: (optional, accepted values are yes or no, yes is default)
        permanent: (optional, accepted values are yes or no, no is default)
        state: (required, accepted values are enabled or disabled, there is no default value)

```

Example:
```
    firewalld_zone_interfaces:          
      - interface: enp0s8
        zone: public
        immediate: yes
        permanent: yes
        state: enabled
      - interface: enp0s3
        zone: public
        immediate: yes
        permanent: yes
        state: enabled
    
```

---

The following variables are used to define the source of a zone:

```
    firewalld_zone_source:
      public:
        zone: (required, zone name)
        source: (required, array of sources e.g. [ 192.168.0.1/24, 10.0.0.100 ])
        permanent: (optional, only values: true|false, default: true)
        immediate: (optional, only values: true|false, default: true)
        state: (optional, only values: enabled|disabled, default: enabled)
```

---

The following variables are used to define a service rule:

```
    firewalld_service_rules: 
      name:
        service: (optional, default: use name if service is not defined)
        zone: (optional, default: public)
        permanent: (optional, only values: true|false, default: true)
        immediate: (optional, only values: true|false, default: true)
        state: (optional, only values: enabled|disabled, default: enabled)

```

Examples:
```
    firewalld_service_rules: 
      ssh:
        zone: public
        permanent: true
        immediate: true
        state: enabled
```
or
```
    firewalld_service_rules:
        ssh_trusted:
            service: ssh
            zone: trusted
            state: enabled
        ssh_public:
            service: ssh
            zone: public
            state: enabled
```

---

The following variables are used to define a port rule:

```
    firewalld_port_rules:
      name:
        port: (required, port or port range)
        protocol: (optional, only values: tcp|udp, default: tcp)
        zone: (optional, default: public)
        permanent: (optional, only values: true|false, default: true)
        immediate: (optional, only values: true|false, default: true)
        state: (optional, only values: enabled|disabled, default: enabled)
```

---

The following variables are used to define a rich rule:

```
    firewalld_rich_rules: 
      name:
        rule: (required, a complete rule in firewalld rich language)
        zone: (optional, default: public)
        permanent: (optional, only values: true|false, default: true)
        immediate: (optional, only values: true|false, default: true)
        state: (optional, only values: enabled|disabled, default: enabled)
```

Handlers
--------

These are the handlers that are defined in this role:

* restart firewalld
* restart rsyslog

Example Playbook
----------------

```
    - hosts: foo
      become: yes
      roles:
        - role: firewalld
          vars:
            default_zone: public
            firewalld_zone_interfaces:          
            - interface: enp0s8
                zone: public
                immediate: yes
                permanent: yes
                state: enabled
            - interface: enp0s3
                zone: trusted
                immediate: yes
                permanent: yes
                state: enabled        
            firewalld_zone_source:
            trusted:
                zone: trusted
                source:
                - "192.168.1.0/24"
                - "10.0.16.12"
                permanent: true
                immediate: true
                state: enabled
            firewalld_service_rules:
            ssh:
                zone: public
                permanent: true
                immediate: true
                state: enabled
            firewalld_port_rules:
            smtp:
                port: 25
                protocol: tcp
                zone: public
                permanent: true
                immediate: true
                state: enabled
            firewalld_rich_rules:
            ftp_audit:
                rule: 'rule service name="ftp" audit limit value="1/m" accept'
                zone: public
                permanent: true
                immediate: true
                state: enabled
```

License
-------

MIT
