---
description: This page describes the Sia storage destination
---

# Sia Destination

{% hint style="warning" %}
The Sia destination is currenctly deprecated as it is incompatible with the current version of the network.
{% endhint %}

Duplicati supports backups to the [Sia network](https://sia.tech) which is a large-scale decentralized storage network. To use the Sia destination, use a this URL format:

```
sia://<host>:<port>/<path>
  ?sia-password=<password>
```

If the host is supporting unauthenticated connections, you can omit the password. The default port is `9980` if none is supplied and the default path is `/backup` if none is supplied.

## Advanced options

To adjust the amount of redundancy in the Sia network, use the option `--sia-redundancy`. Note that this value should be more than `1`.

