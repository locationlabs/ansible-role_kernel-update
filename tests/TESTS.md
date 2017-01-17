# Tests

Here are some playbooks that support different tests, and some code to support running
them in Vagrant.

## Running tests

The tests are written as Ansible playbooks that operate on `hosts: all`. The tests don't
assume much about the machine they're operating on, but it should be a clean machine if
possible.

You could use just the playbooks, but there's also a provided Vagrantfile, ansible
inventory, and test runner shell script.

To run all tests:

  ./run_tests.sh

To run a specific test:

  TESTS=upgrade ./run_tests.sh

## Testing on vagrant

If you have issues with restarting vagrant boxes due to mounting issues you might need
to check your virtualbox and vagrant versions are up to date. If that doesn't solve the
issue you could also check your vagrant plugins.

    > vagrant plugin list
    vagrant-cachier (1.2.1)
    vagrant-hostmanager (1.8.5)
    vagrant-multiprovider-snap (0.0.14)
    vagrant-share (1.1.6, system)
    vagrant-vbguest (0.13.0)