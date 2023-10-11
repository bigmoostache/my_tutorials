## Generating an RSA SSH Key with `ssh-keygen`

SSH (Secure Shell) is a protocol for securely accessing remote systems, and one of the primary authentication methods for SSH is through the use of key pairs. Here, we'll guide you through generating an RSA SSH key pair using `ssh-keygen`.

!!! note
    Before generating an SSH key, ensure you have `ssh-keygen` installed. It comes as a part of the OpenSSH package. Here's how to install it:

    ### On Ubuntu/Debian:

    ```bash
    sudo apt-get update
    sudo apt-get install openssh-client
    ```

    ### On CentOS/Red Hat:

    ```bash
    sudo yum install openssh-clients
    ```

    ### On macOS:

    MacOS typically comes with OpenSSH preinstalled, so `ssh-keygen` should already be available. If it's not, consider using [Homebrew](https://brew.sh/):


## Generating the RSA Key Pair

Once you've ensured `ssh-keygen` is installed, follow these steps:

1. **Open a terminal**.

2. **Run the `ssh-keygen` command**:
   
```bash
ssh-keygen -t rsa -b 4096
```

- `-t rsa` specifies the key type as RSA.
- `-b 4096` specifies the key length as 4096 bits, making it more secure.

3. **Choose a location to save the keys**:
   
When prompted with `Enter file in which to save the key`, you can press `Enter` to use the default location (`~/.ssh/id_rsa`) or specify a custom location.

4. **Set a passphrase** (optional but recommended):

Setting a passphrase adds an extra layer of security. Whenever you use your SSH key, you'll also be asked to enter the passphrase. Usually, you can leave this blank and press `Enter` to skip this step.

5. **Keys are generated!**:

At this point, you should have two key files in the specified directory (or the default location):

- **Private key**: `~/.ssh/id_rsa`
- **Public key**: `~/.ssh/id_rsa.pub`

!!! warning
    **Never share your private key (`id_rsa`)**. The public key (`id_rsa.pub`) is the one you'll provide to remote systems or services to authenticate yourself.


You now have an RSA SSH key pair. You can add your public key (`~/.ssh/id_rsa.pub`) to the `~/.ssh/authorized_keys` file on remote systems or services to allow passwordless authentication using your private key.

Remember: Always keep your private key secure and never share it with anyone. If you ever believe your private key has been compromised, generate a new key pair immediately.