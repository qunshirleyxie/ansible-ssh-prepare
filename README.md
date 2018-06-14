# Ansible SSH Prepare
Ansible Playbook to prepare your Ansible hosts with SSH connectivity by performing the following tasks:

1. Get the host key for all hosts (using `ssh-keyscan`)
2. Remove any entries in `known_hosts` for the specific hosts. This guarantees a clean `known_hosts`. It only removes entries related to the hosts in your `inventory` file.
3. Add host keys to your `known_hosts` file on your localhost
4. Copy user SSH public key to the `authorized_keys` file on your hosts (using ssh-copy-id)

The `dig` command is used in the Playbook to retrieve the IP address from the FQDN. This allows us to save the key into the `known_hosts` using both the FDQN and IP address. This is useful so you can use either the IP or FQDN to ssh to the host.

## Example
Run the playbook with the following command so that Ansible prompts you for the user password (using `--ask-pass` argument). This will be used when copying the SSH public key for the user to the host. You need to do this so that you prevent future requests for password.
```
ansible-playbook -i inventory --ask-pass ssh-prepare.yml
```

## References
The original idea was found at [Stackoverflow](http://stackoverflow.com/a/39083724). I have done some improvements and adjustments.
