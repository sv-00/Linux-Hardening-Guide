# Linux Hardening Guide

A step-by-step guide to securing a Linux VPS by implementing security best practices, reducing attack surfaces, and enhancing overall system resilience.

![image](https://github.com/user-attachments/assets/e703537d-3361-4e2c-9386-8391306f125f)

## 1) Enable Automatic Updates

Keeping your system updated is essential for security. Enable automatic updates using:

```sh
sudo apt install unattended-upgrades
sudo dpkg-reconfigure --priority=low unattended-upgrades
```

Select **Yes** when prompted.

## 2) Set Up a User Account with Limited Permissions

Using a non-root user enhances security:

```sh
sudo adduser username
sudo usermod -aG sudo username
```

## 3) Setup Public & Private Keys

### Create the `.ssh` directory and set permissions:

```sh
mkdir ~/.ssh && chmod 700 ~/.ssh
```

### Generate SSH key pair:

```sh
ssh-keygen -b 4096
```

### Upload the generated public key to the server:

For Windows:

```sh
scp $env:USERPROFILE/.ssh/id_rsa.pub username@server_ip:~/.ssh/authorized_keys
```

For Linux:

```sh
ssh-copy-id username@server_ip
```

## 4) Lock Down Logins

Edit the SSH configuration file:

```sh
sudo nano /etc/ssh/sshd_config
```

Modify the following settings:

```
Port 717  # Change the SSH port from default 22
AddressFamily inet  # Use only IPv4
PermitRootLogin no  # Disable root login
PasswordAuthentication no  # Disable password authentication (use SSH keys)
```

Restart SSH service:

```sh
sudo systemctl restart ssh
```

Now, log in using:

```sh
ssh username@server_ip -p 717
```

## 5) Configure the Firewall

### Check for open ports:

```sh
sudo ss -tupln
```

### Install and configure UFW (Uncomplicated Firewall):

```sh
sudo apt install ufw
sudo ufw allow 717  # Allow SSH port
sudo ufw enable
```

### Block ICMP (Ping) requests:

```sh
sudo nano /etc/ufw/before.rules
```

Find the section labeled `# ok icmp codes INPUT` and add the following lines:

```sh
-A ufw-before-input -p icmp
--icmp-type echo-request -j DROP
```

Reload UFW to apply changes:

```sh
sudo ufw reload
```

## Conclusion

By following these steps, you significantly enhance your Linux VPS security, reducing its attack surface and mitigating potential threats. Stay vigilant and keep your system updated!

