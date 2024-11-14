---
description: This page describes the different channels and how releases are used.
---

# Release channels and versions

When using any software, it is important to use an updated version, but each update also carries a risk of containing a bug or change that requires intervention on the machine. To balance these two parameters, Duplicati uses channels to push updates at different speeds. Builds start out as canary builds and once stability is achieved they move up through the channels unless a breaking issue is discovered.

Installations work the same on any channel so you may choose to uninstall one version and install another. By default, the built-in update checker will use the channel of the package you installed to check for new versions.

## Stable channel

The stable channel is the slowest moving channel. Builds in this channel are considered well tested and robust. This channel is recommended for most users.

## Beta channel

The beta channel is generally used as a staging ground before moving to a stable release. Releases in this category are more frequent than the stable, but is also usually a slowly moving channel. This channel is recommended for users who wants to be on top of new developments. For larger installations, it may make sense to have a few machines on the beta channel to discover changes before affecting the entire setup.

## Experimental channel

Releases in the experimental channel usually contains a new experimental setting or algorithm that is not yet battle proven across a large set of system. These releases are generally considered safe for general use but may contain features that will be removed again or are not working in all environments.

## Canary channel

The canary builds are regular builds that are extracted from the latest developments. The releases in this channel can have bugs and are generally not recommede for production use. These builds are usually the first time the changes are tested on machines that are not managed by developers. These builds are mostly recommeded for users that want to follow the development closely and give feedback on development direction and impact feature development.

