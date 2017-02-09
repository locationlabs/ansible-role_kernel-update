Kernel Update
=============

Update Ubuntu Linux kernel as required for Location Labs.

This role currently supports upgrading the linux kernel for a few Ubuntu LTS
releases in order to prepare for installing (and to address known issues
with) docker. It follows as closely as possible the documentation provided
by ubuntu and docker.

  * https://wiki.ubuntu.com/Kernel/LTSEnablementStack#Server
  * https://docs.docker.com/engine/installation/linux/ubuntu/#/prerequisites
  * https://github.com/locationlabs/ansible-role_docker

When updating the linux kernel, this role is able to write a file to the
host documenting the kernel version used to boot before running the update.
If desired the user can use this role to rollback to the version stored in
the file or to another version which is manually set.

While the main reason we have this role is to upgrade for usage with
docker, we do not install any docker component as a part of this role. We
are assuming all boxes that run this role will want a kernel package
configuration similar to what is required by docker.

Cleanup of old kernels or other kernel packages is not a part of this role.

Supported Ubuntu Versions
-------------------------

Ubuntu versions upgrade support:

    14.04, 12.04

Ubuntu versions rollback support:

    14.04, 12.04

The majority of testing has been done with:

    14.04, 12.04

There is some language in the code suggesting support for a few other versions
of ubuntu but there hasn't been much testing on them.

    16.04, 13.10, 13.04


Role Variables
--------------

The role has many variables used for internal purposes, but it is recommended that users of
the role stick with the variables defined as the primary interface for the role listed below.

Role specific variables:

  - `kernel_update_cgroup_lite_pkg_state`: When installing on an Ubuntu 13.10 host, the role
    will install the `cgroup-lite` package to provide the required cgroups support. This variable
    can be set to `latest` - the default - or to `present`. In the former case, the package will
    be updated, if necessary, when the role is run. In the latter, the package will only be added
    if it is not present.
  - `kernel_update_config_prefix_grub_default`: the prefix to use when creating the file containing
    the `GRUB_DEFAULT` configuration.
  - `kernel_update_kernel_pkg_state`: This parameter works the same way as `cgroup_lite_package_state`,
    except controlling all kernel packages. The primary packages are `linux-*` type packages
    (i.e. `linux-image-extra-*`, `linux-image-generic-*`, `linux-headers-*`, etc.) installed based
    on the Ubuntu version.
  - `kernel_update_kernel_version_file`: The location of a file to write the pre update (upgrade or
    or rollback) kernel version to so that we can rollback across hosts with different kernel versions.
    It is expected that the path to the file already exists. By default the file will get generated in
    `/var/lib/misc` as per [Filesystem Hierarchy Standard][3]. See `kernel_update_store_kernel_version`
    for details on when the file is written.
  - `kernel_update_rollback`: A boolean indicating if the role is going to preform a rollback.
    The default is `False` indicating the role will perform an upgrade of the kernel unless changed.
    If set to `True` this role will rollback to a kernel version specified in the
    `kernel_update_kernel_version_file` if it exists, unless a user explicitly defines the
    `kernel_update_rollback_kernel_version`.

Optional role specific variables (without an entry in defaults):

  - `kernel_update_rollback_kernel_version`: A string indicating what kernel version to rollback to.
    The string is the kernel release that would get found when running `uname -r`. This value will
    default to the contents of `kernel_update_kernel_version_file` unless explicitly defined. The
    role will make an effort to confirm that the kernel version provided is valid, but care should
    be used when setting this value manually.
  - `kernel_update_store_kernel_version`: A boolean indicating if the current kernel version should
    get written to a file on the host. By default a file will only get written if this value has been
    set to `True` or the `kernel_update_kernel_version_file` doesn't exist yet.
    See `write-kernel-version.yml` a for discussion of a possible issue with setting to `True` when
    deploying to a multi box environment.

Shared role variables:

  - `apt_cache_valid_time`: Apt cache is updated if the last check is older than
    this duration (in seconds). Defaults to 1 day.
  - `ssh_port`: Which port to poll in order to confirm the target box is accessible via ssh
    after a restart.


Grub
----

This role relies on details of how /boot/grub/grub.cfg is generated on a few Ubuntu versions
as well as some [documented][4] ([d1][5],[d2][6],[d3][7]) functionality to run properly. It
is possible there are some issues that would make this role not portable to some particular
configuration of an Ubuntu version (for instance if you have a different version of grub running
on 14.04 than is expected) so take steps to make sure this works for your use case.

This role assumes the following version mappings:

  - Ubuntu 12.04: Grub 1.99
  - Ubuntu 14.04: Grub 2.02

We might need a playbook that checks the grub version on each host.

  - get the version of grub that is in use

        # see: info grub-mkconfig
        grub-mkconfig -v

### Grub Configuration

When this role sets grub to load a version of the kernel which isn't the most recent it
does so by generating a file in /etc/default/grub.d. This only occurs in the event of a
rollback. An upgrade will remove the generated file so that the most recent kernel is
used to boot with. *NOTE* This assumes that your /etc/default/grub configuration sets
`GRUB_DEFAULT=0`.

We do this in order to allow other methods of updating the kernel to not require
additional steps for the new kernel to get booted (i.e. removing the explicitly
defined kernel version configuration).

Testing
-------

There's a directory "tests" with some Ansible playbooks that can be used for verifying role
behavior. See tests/TESTS.md for more information.

License
-------

Apache v2.0

Notes
-----

I've created a gist with links to some other documentation that users of the role may find useful.

  * https://gist.github.com/yarloocll/2fb855eb762ebb6d6397d6598594f404

[//]: # "Links"

[1]: https://wiki.ubuntu.com/Kernel/LTSEnablementStack#Server
[2]: https://docs.docker.com/engine/installation/linux/ubuntulinux/#/prerequisites-by-ubuntu-version
[3]: https://refspecs.linuxfoundation.org/FHS_3.0/fhs-3.0.html#varlibVariableStateInformation
[4]: https://help.ubuntu.com/community/Grub2
[5]: https://help.ubuntu.com/community/Grub2/Submenus#Submenu_Display
[6]: https://www.gnu.org/software/grub/grub-documentation.html
[7]: https://www.gnu.org/software/grub/manual/html_node/Simple-configuration.html