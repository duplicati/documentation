---
description: This page describes the OpenStack storage destination
---

# OpenStack Destination

Duplicati supports storing files with OpenStack, which is a large-scale object storage, similar to S3. With OpenStack you store "objects" (similar to files) in "containers" which define various properties shared between the objects. If you use a `/` in the object prefix, they can be displayed as virtual folders when listing them.

## OpenStack v2

If you are using OpenStack with version 2 of the protocol, you can either use an API key or a username/password/tenant combination. To use the password based authentication, use a URL format like this:

```
openstack://<container>/<prefix>
  ?auth-username=<username>
  &auth-password=<password>
  &openstack-tenant-name=<tenant>
  &openstack-authuri=<url to auth endpoint>
```

If you are using an API key, leave out the `--auth-password`  and `--openstack-tenant-name` parameters and add in `--openstack-apikey=<apikey>`.

## OpenStack v3

If you are using OpenStack with version 3 of the protocol, you must supply: username, password, domain, and tenant name:

```
openstack://<container>/<prefix>
  ?auth-username=<username>
  &auth-password=<password>
  &openstack-tenant-name=<tenant>
  &openstack-domain-name=<domain>
  &openstack-authuri=<url to keystone server>
  &openstack-version=v3
```

## Region selection

The authentication response will contain a set of endpoints to be used for actual transfers. In some cases, this response can contain multiple possible endpoints, each with a different region. To prefer a specific region, supply this with `--openstack-region`. If any of the returned endpoints have the same region (case-insensitive compare), the first endpoint matching will be selected. If no region is specified, or no region matches, the first region in the response is used.
