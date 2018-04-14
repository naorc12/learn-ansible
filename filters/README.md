# Filters

This file contains Ansible filters


* Convert hostname to IP address

```
from ansible.errors import AnsibleError
import socket

def hostname_to_ip(self, hostname):
    """Returns an IP address given a hostname"""
    try:
        return socket.gethostbyname(str(hostname))
    except socket.error as e:
        raise AnsibleError(e)
```

* Convert IP address to hostname

```
from ansible.errors import AnsibleError
import socket

def ip_to_hostname(self, ip):
        """Returns a hostname given an IP address"""
        try:
            return socket.gethostbyaddr(ip)[0]
        except (socket.error, socket.herror) as e:
            raise AnsibleError(e)
```

* Apply Python string formatting on an object

```
def map_format(value, pattern):
    """
    {{ "%s - %s"| map_format("Hello?", "Foo!") }}
    -> Hello? - Foo!
    """
    return soft_unicode(pattern) % (value)
```
