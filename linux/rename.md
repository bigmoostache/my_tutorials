1. Decide on a new unique hostname, for instance: `c2-7-gra11-new`.

2. Change the hostname temporarily:
    ```bash
    sudo hostname c2-7-gra11-new
    ```

3. Update `/etc/hostname` with the new name so it remains persistent across reboots:
    ```bash
    echo "c2-7-gra11-new" | sudo tee /etc/hostname
    ```

Now, you can try joining the node to the Kubernetes cluster again using `kubeadm join`.

This method avoids a reboot, but it's a good idea to eventually reboot the machine to ensure all services pick up the new hostname properly.