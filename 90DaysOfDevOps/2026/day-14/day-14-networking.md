# Networking Fundamentals & Hands-on Checks
Networking is system of interconnected nodes that are used to communicate with each other.

## OSI (Open System Interconnection)
- OSI consists of 7 layers, it is model that is used for communication.

 `Layer-7 Application Layer`

Application layer means end user where the appcn like  Google, whatsapp etc. I t provides services like web browsing HTTP, Email SMTP or File Transfer FTP

 `Layer-6 Presentation Layer`

TRanslates  data between applcn and network formats.

It handles encryption , compression and data formatting.

 `Layer-5 Session Layer`:

This layer manages sessions (connections) between applications.

It esatablish, maintain, terminates the sessions i.e Login session.

 `Layer-4 Trasport Layer`:

Provides end-to-end communication and error recovery.

Protocols such as Transmission Control Protocol (TCP) and User Datagram Protocol (UDP) work here.

 `Layer-3 Network Layer`:

Responsible for logical addressing and routing data between different networks.

Protocols like Internet Protocol (IP) operate at this layer.

 `Layer-2 Data Link Layer`:

Ensures reliable data transfer between two directly connected devices.

It handles MAC addressing and error detection (e.g., Ethernet frames).
  
 `Layer-1 Physical Layer`:

Handles the physical connection between devices.

It transmits raw bits (0s and 1s) over cables, radio waves, or other hardware mediums.

------------------------------

## Networking Commands
- `hostname -I or ip addr show` - prints the systems ip addr, ip addr show : shows network interface and associated IP addr
<img width="932" height="381" alt="image" src="https://github.com/user-attachments/assets/68450e32-2fb2-4c53-b970-9a4ee76f1cc3" />

- `ping target-name`: pings the network by sending and recieving packets. Checks reachability
<img width="814" height="264" alt="image" src="https://github.com/user-attachments/assets/5bc919df-72ba-47af-8984-b87a2564362a" />

- `traceroute target`: displays step-by-step transfer of my network connection and the hops
<img width="975" height="379" alt="image" src="https://github.com/user-attachments/assets/381977c4-8f83-4840-9dfa-8deaaf033d50" />


- `ss -tulpn (or netstat -tulpn)`: shows active network sockets
<img width="987" height="316" alt="image" src="https://github.com/user-attachments/assets/93f98700-c975-4443-bee2-32cb8dce4df3" />

- `dig <domain> or nslookup <domain>`: dig is a DNS lookup tool used to query DNS servers for information about domain names.
It provides detailed results like IP address, mail servers, name servers, and query time.

nslookup is another command-line tool used to check DNS records.
It is simpler and commonly used for quick domain-to-IP checks.

<img width="703" height="618" alt="image" src="https://github.com/user-attachments/assets/592bcd86-77dd-4190-8495-57713dc11577" />


- `curl -I <http/https-url>`: sends a HEAD request to the website and shows only the HTTP response headers.

If you get a status like 200, 301, or 302, the site is reachable; if you see connection errors, it is not reachable.

- `netstat -an | head`:shows the first few active network connections and listening ports in numeric form (IP addresses and port numbers).

You can roughly check how many connections are ESTABLISHED (active connections) vs LISTEN (services waiting for connections)
<img width="781" height="226" alt="image" src="https://github.com/user-attachments/assets/eddfadda-f575-497d-8a87-47305058abea" />

------------------------

### Port Probe & Interpret
`
Identify one listening port from ss -tulpn (e.g., SSH on 22 or a local web app).
From the same machine, test it: nc -zv localhost <port> (or curl -I http://localhost:<port>).
Write one line: is it reachable? If not, what’s the next check? (e.g., service status, firewall).
`


<img width="711" height="518" alt="image" src="https://github.com/user-attachments/assets/0844dc3e-60ab-49a2-b447-2a6d12dcb1e3" />

- WHile performing `curl -I http://localhost:80 ` it is displaing 200 status OK! and prints info about server , date , last modified etc.
