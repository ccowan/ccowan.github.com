---
title: "Firewall Port Forwarding with Puppet"
layout: "post"
tags: ["post","node.js","testing"]
---

Recently, we started managing our boxes with Puppet. Part of setting that up was configuring the firewall using the [Puppet Firewall module](https://github.com/puppetlabs/puppetlabs-firewall). Since we are running Node.js as an unprivileged user we needed to forward both port `80` and `443` to `8000` and `8443` respectively. I found plenty examples of setting this up with iptables but I didn't come across a Puppet example. Enjoy... :D

```ruby
class fw::nodejs {
  Firewall {
    require => undef,
  }

  firewall { '100 accept port 8000 and 8443':
    dport  => [8000, 8443],
    action => accept,
    proto  => 'tcp'
  }

  firewall { '101 forward port 80 to 8000':
    table   => 'nat',
    chain   => 'PREROUTING',
    jump    => 'REDIRECT',
    proto   => 'tcp',
    dport   => 80,
    toports => 8000
  }

  firewall { '101 forward port 443 to 8443':
    table   => 'nat',
    chain   => 'PREROUTING',
    jump    => 'REDIRECT',
    proto   => 'tcp',
    dport   => 443,
    toports => 8443
  }

}
```

Keep in mind you will still need to setup your firewall config for persisting the firewall as outlined on the [Puppet Firewall module](https://github.com/puppetlabs/puppetlabs-firewall) page. 

ProTip! If you want to see if the routing was setup, use the following command: 

```sh
sudo iptables -t nat -L
```
