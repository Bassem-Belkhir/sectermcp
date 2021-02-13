# sectermcp
## Description
Project of getting a self installed and configured secured VPS with a control panel that allow to create web sites manage DNS create email addresses manage server engines and databases, cron jobs...

I'm not a security expert so any help would be greatly appreciated. :heart_eyes:


## manual commands

### Install and configure the firewall
Connect to the server and type in these commands
```
sudo apt-get update
sudo apt-get install ufw
```

ufw has the ability to deny connections from an IP address that has attempted to initiate 6 or more connections in the last 30 seconds.
```
sudo ufw limit 22/tcp
```

By default, UFW is set to deny all incoming connections and allow all outgoing connections.
```
sudo ufw allow 80/tcp  
sudo ufw allow 443/tcp
sudo ufw enable
```
```
sudo ufw default deny incoming  
sudo ufw default allow outgoing
sudo ufw reload
```

Check the ufw config
```
sudo ufw status
```

### Create SSH key to connect to the server
Go back to your terminal
```
ssh-keygen -f ~/custom_filename -t ecdsa -b 521
```
Why this command ?
it will be clear if you visit this link [ssh-keygen - Generate a New SSH Key # Choosing an Algorithm and Key Size](https://www.ssh.com/ssh/keygen/#choosing-an-algorithm-and-key-size)

Copying the Public Key to the Server
```
ssh-copy-id -i ~/.ssh/the_public_key_custom_filename user@host
```

Test the new connection config by trying to connect via SSH
```
ssh user@host
```
**!Make sure to have a backup key,, you won't be able to connect if you lost the key file!**

Now in in /etc/sshd_config ( in the VPS )
```
ChallengeResponseAuthentication no
PasswordAuthentication no
UsePAM no
PermitRootLogin no
```
save and exit
```
systemctl restart sshd
```
or it might be ssh
```
systemctl restart ssh
```
Exit the server and reconect to test the config.
To be sure that you cannot connect without the key try connecting with another pc or using a VM.

### Edit /etc/sysctl.conf
Uncomment these lines

##### Reverse path filtering
By enabling reverse path filtering, the kernel will do source validation of the packets received from all the interfaces on the machine. This can protect from attackers that are using IP spoofing methods to do harm.
```
net.ipv4.conf.default.rp_filter = 1
net.ipv4.conf.all.rp_filter = 1
```
##### Log martian packets
A martian packet is an IP packet which specifies a source or destination address that is reserved for special-use by Internet Assigned Numbers Authority (IANA).
Often martian and unroutable packet may be used for a dangerous purpose.
```
net.ipv4.conf.default.log_martians = 1
net.ipv4.conf.all.log_martians = 1
```
##### Disable ICMP redirects
[What are ICMP redirects? Should they be blocked?](https://askubuntu.com/questions/118273/what-are-icmp-redirects-and-should-they-be-blocked)
```
net.ipv4.conf.all.accept_redirects = 0
net.ipv4.conf.default.accept_redirects = 0
net.ipv4.conf.all.secure_redirects = 0
net.ipv4.conf.default.secure_redirects = 0
net.ipv6.conf.all.accept_redirects = 0
net.ipv6.conf.default.accept_redirects = 0
```
To disable ICMP redirect sending when on a non router:
```
net.ipv4.conf.all.send_redirects = 0
net.ipv4.conf.default.send_redirects = 0
```
To disable ICMP echo (aka ping) requests:
```
net.ipv4.icmp_echo_ignore_all = 1
net.ipv6.icmp.echo_ignore_all = 1
```
Then apply the kernel parameter modifications:
```
sudo sysctl -p
```

for further info visit this link on [arch wiki](https://wiki.archlinux.org/index.php/sysctl)

##### Prevent IP Spoof 
On /etc/host.conf
```
order bind,hosts
multi on
nospoof on
```

##### Install enable and start fail2ban
```
sudo apt install fail2ban
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```
and to be sure fail2ban is installed and running
```
sudo systemctl status fail2ban
```

Fail2ban scans log files (e.g. /var/log/apache/error_log) and bans IPs that show the malicious signs -- too many password failures, seeking for exploits, etc. Generally Fail2Ban is then used to update firewall rules to reject the IP addresses for a specified amount of time, although any arbitrary other action (e.g. sending an email) could also be configured. Out of the box Fail2Ban comes with filters for various services (apache, courier, ssh, etc).

Fail2Ban is able to reduce the rate of incorrect authentications attempts however it cannot eliminate the risk that weak authentication presents. Configure services to use only two factor or public/private authentication mechanisms if you really want to protect services.

[source](https://www.fail2ban.org/wiki/index.php/Main_Page)



#### Tasks
- [ ] Preapare security manual commands.
- [ ] See about [trip wire](https://github.com/Tripwire/tripwire-open-source) for monitoring.
- [ ] Prepare the installation of a secured mail server.
- [ ] Preapare .sh auto install file.
- [ ] Create an admin Interface.
- [ ] Add the code to the .sh file to include the admin interface.
