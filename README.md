# Ansible SSH Prepare
Ansible Playbook to prepare your Ansible hosts with SSH connectivity.

Tools required:

- ssh-keyscan: utility for gathering the public SSH host keys of a number of hosts. It was designed to aid in building and verifying ssh_known_hosts files.
- ssh-keygen: ssh-keygen generates, manages and converts authentication keys for ssh
- ssh-copy-id: script that uses ssh to log into a remote machine using password authentication; it will create the `~/.ssh` folder, sets the permissions to remove group writability (prevents security vulnerability), creates the file `~/.ssh/authorized_keys` if not existing and sets permissions on this file too. Lastly, it copies the user public key and appends it to the end of the `~/.ssh/authorized_keys` file.

Steps:

1. Get the host key for all hosts (using `ssh-keyscan`)
2. Remove any entries in `known_hosts` for the specific hosts. This guarantees a clean `known_hosts`. It only removes entries related to the hosts in your `inventory` file.
3. Add host keys to your `known_hosts` file on your localhost
4. Copy user SSH public key to the `authorized_keys` file on your hosts (using ssh-copy-id)

The `dig` command is used in the Playbook to retrieve the IP address from the FQDN. This allows us to save the key into the `known_hosts` using both the FDQN and IP address. This is useful so you can use either the IP or FQDN to ssh to the host.

## Example
Run the playbook with the following command. Use the `--ask-pass` argument to have Ansible prompt you for the password, which will be used when copying the SSH public key for the user to the host. You need to do this so that you prevent future requests for password.
```
ansible-playbook -i inventory --ask-pass ssh-prepare.yml
```

## References
I found the original idea at [Stackoverflow](http://stackoverflow.com/a/39083724), but I have done many improvements and adjustments.

Wonderful overview on [host keys](https://www.ssh.com/ssh/host-key).

Additional recommended reading on SSH can be found here: 
[ssh essentials - working with ssh servers clients and keys](https://www.digitalocean.com/community/tutorials/ssh-essentials-working-with-ssh-servers-clients-and-keys).

## SSH Tips and Tricks
**disconnect**
To disconnect from a hanging SSH connection use the following key-command: `~.`

**log level**
You can increase the log level for the ssh connection to determine what it is actually doing. This is very helpful in times when you simply cannot connect to the server and need to understand what it is trying to do or where it is failing. You can use `-v` for level 1 debugging, `-vv` for level 2, and `-vvv` for level 3 debugging. Each level provides more information. Start with `-v` and see if that is sufficient to understand your connection issue. Increase it as necessary.
```bash
ssh -v(vv) user@server
```

For a more permanent log level setting, you can add the following line to your `~/.ssh/config` file. In this example we are using `DEBUG` level but you may choose any from the list below.
```
LogLevel DEBUG
```

Alternatively, you can dynamically increase or decrease the logging level for SSH by using the following key sequences. This will not modify your `~/.ssh/config` file. It simply sets the logging level in memory.

- increase the log level, use `~v` (lowercase v) at a new prompt
- decrease, use `~V` (uppercase V) at a new prompt.

Here are the list of log levels:
- QUIET
- FATAL
- ERROR
- INFO
- VERBOSE (the apparent default)
- DEBUG
- DEBUG1
- DEBUG2
- DEBUG3
