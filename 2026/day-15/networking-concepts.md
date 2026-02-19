# Networking Concepts: DNS, IP, Subnets & Ports

**Task 1: DNS – How Names Become IPs**

```
1. Explain in 3–4 lines: what happens when you type google.com in a browser?
2. What are these record types? Write one line each:
    A, AAAA, CNAME, MX, NS
3. Run: dig google.com — identify the A record and TTL from the output
```

- When we type `google.com` in a browser in the background the DNS translates the `google.com` domain name to machine readable IP addr(ex. 192.5.23.1)
- It recieves the IP addr(A/AAA record) -> After that the browser connects that IP add and loads the site

   *DNS RECORD TYPES*
- **A Record (Address)**: Maps a domain or subdomain to a 32-bit IPv4 address (e.g., example.com 
 192.0.2.1).
- **AAAA Record (IPv6 Address)**: Maps a domain/subdomain to a 128-bit IPv6 address.
- **CNAME Record (Canonical Name)**: Forwards or aliases one domain/subdomain to another domain name (e.g., blog.example.com 
 example.com), rather than an IP address.
- **MX Record (Mail Exchange)**: Directs email to the correct mail server for the domain, usually assigned a priority.
- **TXT Record (Text)**: Stores arbitrary text, widely used for domain verification, SPF, DKIM, and DMARC email security.
- **NS Record (Name Server)**: Specifies the authoritative name servers that hold the DNS records for a zone.


`dig google.com`: google.com.             0       IN      A       142.251.223.110

TTL- 0   &&    A record IP: 142.251.223.110


------------------------------

**Task 2: IP Addressing**

```
What is an IPv4 address? How is it structured? (e.g., 192.168.1.10)
Difference between public and private IPs — give one example of each
What are the private IP ranges?
10.x.x.x, 172.16.x.x – 172.31.x.x, 192.168.x.x
Run: ip addr show — identify which of your IPs are private
```
- Ipv4 is a 32-bit addr used to identify device on a network. Written in four octates seperated by dots (e.g. 192.168.1.2 , ranging from 0-255)

- Public Ip: it is a unique and visible to everyone ip. It allows direct internet access
- Private ip: addr used within local or private ntwrks. ex NAT, Route Tables
------------------------------

**Task 3: CIDR & Subnetting**

```
What does /24 mean in 192.168.1.0/24?
How many usable hosts in a /24? A /16? A /28?
Explain in your own words: why do we subnet?
Quick exercise — fill in:
CIDR	Subnet Mask	Total IPs	Usable Hosts
/24	?	?	?
/16	?	?	?
/28	?	?	?
```

- `/24` means the first 24 bits ar efor the netwoork portion an remaining 8 bits are for the host address.
- **Usable ports**
   - `/24` -> 256 total IP's -> 254 usable hosts
   - `/16` -> 65536 total IP's -> 65534 usable hosts
   - `/28` -> 16 total IP's -> 14 usable hosts

     (Formula used- 2^n-2)

- Subnet is a process in which we divide large network into smaller networks to improve security, less IP usage(efficient usage)


------------------------------

**Task 4: Ports – The Doors to Services**

```
What is a port? Why do we need them?
Document these common ports:
Port	Service
22	?
80	?
443	?
53	?
3306	?
6379	?
27017	?
Run ss -tulpn — match at least 2 listening ports to their services

```

- **POrt**: It is like endpoint that alows multiple service to run on an IP Addr

**COMMON PORTS**

Port   |   Service

22     | 	SSH

80     |   	HTTP

443    |  	HTTPS

53     |	DNS

3306   |	MySQL

6379   |	Redis

27017  | 	MongoDB



- Running ports :

tcp        LISTEN      0           4096                   0.0.0.0:22                     0.0.0.0:*

tcp        LISTEN      0           511                    0.0.0.0:80                     0.0.0.0:*


------------------------------

**Task 5: Putting It Together**

```
You run curl http://myapp.com:8080 — what networking concepts from today are involved?
Your app can't reach a database at 10.0.1.50:3306 — what would you check first?
```

- DNS resolution (domain → IP), TCP connection to port 8080, routing via IP addressing, and HTTP protocol communication.
- Check network connectivity (ping), verify port 3306 is listening, confirm firewall/security group rules, and ensure the IP is reachable within the subnet.
