Kernel Update
=============

Ensure kernel is properly configured for Location Labs Ubuntu VMs.

This role currently supports upgrading the linux kernel for a few Ubuntu LTS
releases in order to address known issues with docker. It follows as closely
as possible the documentation provided by [ubuntu][1] and [docker][2].

We are sort of assuming all boxes that run this role will want have kernel packages
installed the same as is required by docker.

Cleanup of old kernels is not yet a part of this role.


Requirements
------------

The primary Ubuntu versions this role supports are:

    12.04, 13.04, 13.10, 14.04, 16.04

The majority of testing has been done with:

    12.04, 14.04

Role Variables
--------------

  - `cgroup_lite_pkg_state` : When installing on an Ubuntu 13.10 host, the role will install the
    `cgroup-lite` package to provide the required cgroups support. This variable can be set to
    `latest` - the default - or to `present`. In the former case, the package will be updated, if
    necessary, when the role is run. In the latter, the package will only be added if it is not
    present.
  - `kernel_pkg_state` : The state of any kernel package installed as a part of this role.
    This parameter works the same way as `cgroup_lite_package_state`, except controlling
    kernel packages.
  - `ssh_port`: Which port to poll in order to confirm the target box is accessible via ssh
    after a restart.
  - `apt_cache_valid_time`: Apt cache is updated if the last check is older than
    this duration (in seconds). Defaults to 1 day.

Testing
-------

There's a directory "tests" with some Ansible playbooks that can be used for verifying role
behavior. See tests/TESTS.md for more information.

[//]: # "Links"

[1]: https://wiki.ubuntu.com/Kernel/LTSEnablementStack#Server
[2]: https://docs.docker.com/engine/installation/linux/ubuntulinux/#/prerequisites-by-ubuntu-version