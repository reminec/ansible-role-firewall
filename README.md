# Ansible Role: Firewall (iptables)
 
[![Build Status](https://travis-ci.org/geerlingguy/ansible-role-firewall.svg?branch=master)](https://travis-ci.org/geerlingguy/ansible-role-firewall)    
Reminec Fork:[![Build Status](https://travis-ci.org/reminec/ansible-role-firewall.svg?branch=master)](https://travis-ci.org/reminec/ansible-role-firewall)    

Installs a iptables-based firewall for RHEL/CentOS or Debian/Ubunty systems.    
The default policy is DROP for INPUT/OUTPUT/FORWARD    

After the role is run, a `firewall` init service will be available on the server. You can use `service firewall [start|stop|restart|status]` to control the firewall.   


## BC with original fork
Because the default policy switch to DROP for OUTPUT    
You need to update your vars.     

Before     
```yaml
firewall_allowed_tcp_ports:
  - 22
  
firewall_allowed_udp_ports:
  - 53
```

After    
```yaml
firewall_allowed_input_tcp_ports:
  - 22
firewall_allowed_output_tcp_ports:
  - 22
  
firewall_allowed_input_udp_ports:
  - 53
firewall_allowed_output_udp_ports:
  - 53
```

## Requirements

- None

## Role Variables

**All variables are optional.**

### Example usage - A list of TCP or UDP ports (respectively) to open to incoming traffic.

    firewall_allowed_tcp_ports:
      - "22"
      - "80"
      ...
    firewall_allowed_udp_ports: []

### Example usage - Forward `src` port to `dest` port, either TCP or UDP (respectively).

    firewall_forwarded_tcp_ports:
      - { src: "22", dest: "2222" }
      - { src: "80", dest: "8080" }
    firewall_forwarded_udp_ports: []
    

### Example usage - Disable firewall for interfaces

```yaml
firewall_disabled_on_iface:
  - openvpn0
```

### Example usage - Allow traffic for ipfailovers

```yaml
firewall_ipfailovers:
  - '1.2.3.4'
```


### Example usage - Enable internal routing for a network

```yaml
firewall_networks:
  - '10.10.10.0/24'
```
### Example usage - Enable multicast on a network

```yaml
firewall_multicast_networks:
  - '224.0.0.0/4'
```

### Example usage - NAT configuration

```yaml
firewall_nat_networks:
  - cidr: '10.10.10.0/24'
    ifaces_allowed:
      - 'vmbr0'
    ifaces_masquered:
      - 'vmbr0'

# Allow forwarding between thoses networks
firewall_forwarded_network: 
  - iface_input: 'venet0'
    iface_output: 'vmbr0'
    
# Describe here your NAT port forwarding 
firewall_nat_forwarded_tcp_ports: 
  - iface:  'vmbr0' # Forward 10042 to 10.10.10.4:22
    src:    '10042'
    addr:   '10.10.10.4'
    dest:   '22'
```

### Default configuration

```yaml
firewall_disable_spoofing: true # Prevent ip spoofing
firewall_drop_all_traffic: true # Drop all non authorized traffic
firewall_accept_established: true # Accept traffic already established
firewall_accept_loopback: true # Accept traffic from loopback interface (localhost).
firewall_accept_icmp_request: true # Accept icmp ping requests.
firewall_accept_ntp: true # Allow NTP traffic for time synchronization.
firewall_enable_dos_limit: true # Deny of service prevention
firewall_log_dropped_packets: true # Log other incoming requests (all of which are dropped) at 15/minute max.
firewall_enable_scan_limit: true # Port scanning prevention
firewall_allowed_input_tcp_ports:
  - "20:21" # FTP
  - "22" # SSH
  - "25" # SMTP
  - "53" # DNS
  - "80" # HTTP
  - "110" # POP3
  - "143" # IMAP
  - "443" # HTTPS
  - "9418" # Git
firewall_allowed_input_udp_ports:
  - "53" # DNS
firewall_allowed_output_tcp_ports:
  - "20:21" # FTP
  - "22" # SSH
  - "25" # SMTP
  - "53" # DNS
  - "80" # HTTP
  - "110" # POP3
  - "143" # IMAP
  - "443" # HTTPS
  - "9418" # Git
firewall_allowed_output_udp_ports:
  - "53" # DNS
  - "123" # NTP
```

## Dependencies
None

## Example Playbook
```yaml
    - hosts: servers
      roles:
         - reminec.iptables
```
## License
MIT / BSD

## Author Information
This role was created in 2014 by [Jeff Geerling](http://jeffgeerling.com/), author of [Ansible for DevOps](http://ansiblefordevops.com/).
Contributor [Reminec](https://github.com/reminec)

