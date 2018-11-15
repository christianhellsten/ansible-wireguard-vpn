# ansible-wireguard-vpn

Ansible + WireGuard = VPN

## Introduction

This Ansible playbook will set up a point-to-point VPN mesh network.

Tested on Debian Stretch, but should also work on Ubuntu.

Please submit a pull request if you find any issues or want to support other platforms.

To customize installation, see roles/wireguard/vars/main.yml and roles/wireguard/defaults/main.yml.

## Prerequisites

- Ansible
- SSH
- Root account

## Usage

First, list all VPN servers, including the VPN IP and public IP, in an Ansible hosts file:

```
server-1 ansible_ssh_host=212.x.x.x vpn_ip=192.168.0.1 public_ip=212.x.x.x
server-2 ansible_ssh_host=212.x.x.x vpn_ip=192.168.0.2 public_ip=212.x.x.x

[vpn-servers]
server-1
server-2
```

Next, install the VPN network:

```
$ ansible-playbook wireguard.yml -i hosts -u root
```

## Troubleshooting

- ping: sendmsg: Required key not available

```bash
# ssh 192.168.0.1
# ping 192.168.0.2
PING 192.168.0.2 (192.168.0.2) 56(84) bytes of data.
From 192.168.0.1 icmp_seq=1 Destination Host Unreachable
ping: sendmsg: Required key not available
```

```bash
# ssh 192.168.0.1
# ping 192.168.0.2
PING 192.168.0.2 (192.168.0.2) 56(84) bytes of data.
From 192.168.0.1 icmp_seq=1 Destination Host Unreachable
ping: sendmsg: Required key not available
```

Solution: Check that the peer server's IP address is in the range defined by AllowedIPs.

- all other issues

Verify that all the peers' private and public keys are correct, check IPs and ports are accessible:

```bash
# wg
interface: wg0
  public key: <check this>
  private key: (hidden) <check this>
  listening port: 50111 <check this>

peer: <check this>
  endpoint: 212.xxx <check this>
  allowed ips: 192.168.0.0/16
  transfer: 0 B received, 4.34 KiB sent
```

## WireGuard cheat sheet

Show WireGuard configuration:

```bash
# wg
```

Stop/Start WireGuard:

```bash
# wg-quick down wg0
# wg-quick up wg0
```

Start the WireGuard service:

```bash
# systemctl start wg-quick@wg0
```

Check WireGuard status:

```bash
# systemctl status wg-quick@wg0
```

Configure WireGuard to start up at boot:

```bash
# systemctl enable wg-quick@wg0
```
