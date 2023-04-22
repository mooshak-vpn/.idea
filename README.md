# Mooshak Idea

The idea is to find a way to route traffic through a ssh server but also avoid ssh dynamic port forwarding.
Generally speaking, the goal is to create an alternative to `sshuttle` that can work on all platforms, but the main target is windows.

Problems of SSH port forwarding:

- SSH dynamic port forwarding creates a socks server that already reduces latency at client side. [read more about tcp over tcp and idea of sshuttle](https://sshuttle.readthedocs.io/en/stable/how-it-works.html)
- The latency reduction however is not the biggest problem. The socks proxy created by SSH does not support UDP packets.


On the other hand, when it comes to windows platform, sshuttle is no longer an option. Mooshak goal is to:

- Create a tun network interface
- Change route to send all network traffic to the tun network interface

Using different Tun-To-Socks tools already implemented by other developers, mooshak will forward traffic to a socks server.

Options on where this socks server can be:

- On local machine if SSH Dynamic Port Forwarding is used
- On remote machine if SSH Port Forwarding (not dynamic) is used

However, multiplexing is lost in this process. The hope is to use socat on local machine for multiplexing connections and send them over to the server side in a single TCP connection. Fortunately, there is already a [windows build for socat](https://github.com/tech128/socat-1.7.3.0-windows).

Since the goal is to avoid using SSH Dynamic Port Forwarding (but to keep it as an option), on the server side we need a socks5 proxy server running, listening to local machine (server) port. Therefore, the flow would be like:

```
        client side       [  tunnel  ]       server side
tun-socks  ->  socat  ->  ssh tunnel  ->  socat  ->  socks server
```

![image](https://raw.githubusercontent.com/mooshak-vpn/.idea/main/route.jpg)



