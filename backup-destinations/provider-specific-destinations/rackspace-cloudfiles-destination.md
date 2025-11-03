---
description: This page describes the Rackspace CloudFiles storage destination
---

# Rackspace CloudFiles Destination

{% hint style="warning" %}
<mark style="background-color:yellow;">Rackspace Cloudfiles is</mark> [<mark style="background-color:yellow;">deprecated</mark>](https://github.com/duplicati/duplicati/issues/6134)<mark style="background-color:yellow;">.</mark>
{% endhint %}

Duplicati supports storing files with Rackspace CloudFiles, which is a large-scale object storage, similar to S3. With CloudFiles you store "objects" (similar to files) in "containers" which define various properties shared between the objects. If you use a `/` in the object prefix, they can be displayed as virtual folders when listing them.

To use CloudFiles, you can use the following URL format:

```
cloudfiles://<container>/<prefix>
  ?cloudfiles-username=<username>
  &cloudfiles-accesskey=<access key>
```

## Using a different API endpoint

The default authentication will use the US endpoint, which will not work if you are a customer of the UK service. To choose the UK account, add `--cloudfiles-uk-account=true` to the request:

```
cloudfiles://<container>/<prefix>
  ?cloudfiles-username=<username>
  &cloudfiles-accesskey=<access key>
  &cloudfiles-uk-account=true
```

If you need to use a specific host, you can also provide the authentication URL directly with the `--cloudfiles-authentication-url` option. If you are providing the URL, the `--cloudfiles-uk-account` option will be ignored.
