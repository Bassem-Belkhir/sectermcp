# sectermcp
## Description
project of getting a self installed and configured secured VPS

## manual commands

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
```

#### Tasks
- [ ] Preapare manual commands.
- [ ] Preapare .sh auto install file.
- [ ] create an admin Interface
- [ ] add the code to the .sh file to include the admin interface
