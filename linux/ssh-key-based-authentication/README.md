# How can I setup SSH key-based authentication between host and target machine?

## Step 1. Generate SSH key pair in the host machine.

1. On the machine from where you want to initiate ssh connection (this could be a machine running Ansible i.e. the control machine), open a terminal or command prompt
2. Execute following command to generate an `SSH key pair`
    ```
    ssh-keygen
    ```

    This command will prompt you to provide a filename for the key pair and an optional passphrase. Press Enter to accept the default file location (~/.ssh/id_rsa) and leave the passphrase empty for now.

    > Note: If you already have an SSH key pair generated, you can skip this step and use your existing key pair.

## Step 2. Copy public key to the target host.

Once the key pair is generated, use the following command to copy the public key to the target host:

```
ssh-copy-id govind@192.9.200.244
```

- Replace govind with your actual username and 192.9.200.244 with the IP address of the target host.
- This command will prompt you for the password of the target user on the host.
- The ssh-copy-id command will automatically copy the public key to the target host and add it to the authorized_keys file in the ~/.ssh directory of the target user. This enables key-based authentication for SSH connections.

## Step 3. Verify SSH key authentication

To verify that SSH key authentication is working correctly, try SSHing into the target host again:

```
ssh govind@192.9.200.244
```

> This time, you should not be prompted for a password. If you can establish an SSH connection without providing a password, then SSH key-based authentication is working as expected.
