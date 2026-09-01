WEEK 1 - DAY 2: DNS AND THE NETWORK LAYER

Date: August 31, 2026
Program Phase: Phase 1, Foundations
Module Focus: DNS Resolution and Network Path Analysis
Analyst: Evans (evxnsdev)


OBJECTIVE

To understand how DNS resolves a domain name into an IP address, understand the basic TCP/IP model governing network communication, and practice manual domain resolution and network path tracing using nslookup and tracert.


1. THEORETICAL FOUNDATION: DNS RESOLUTION PROCESS

When a domain name is requested, resolution typically follows this sequence:

Step 1: The requesting device checks its local cache for a previously resolved answer.
Step 2: If not cached, the query is sent to a configured DNS resolver, commonly the local router or ISP resolver.
Step 3: The resolver performs a recursive lookup on behalf of the client, querying root DNS servers, then top-level domain (TLD) servers, then the domain's authoritative name servers.
Step 4: The authoritative server returns the actual IP address for the requested domain.
Step 5: This answer is returned to the client and typically cached for a period of time to speed up future lookups.

Key DNS record types include: A records (IPv4 address mapping), AAAA records (IPv6 address mapping), CNAME records (domain aliasing), MX records (mail server designation), NS records (authoritative server designation), and TXT records (arbitrary text data, often used for domain verification).


2. THEORETICAL FOUNDATION: THE TCP/IP MODEL

Network communication is commonly understood through four layers:

Application Layer: where protocols such as HTTP and DNS operate directly.
Transport Layer: governs reliable (TCP) or fast, unordered (UDP) data delivery; this is where the TCP three-way handshake occurs.
Internet Layer: handles IP addressing and routing decisions; each hop shown in a traceroute represents a routing decision at this layer.
Network Access Layer: governs the physical transmission of data across hardware such as WiFi or Ethernet.

Ports and protocols are also relevant here: services typically listen on designated ports, such as HTTP on port 80 and HTTPS on port 443, a concept directly relevant to network scanning covered later in this program.


3. PRACTICAL EXERCISE: MANUAL DNS RESOLUTION

Command executed: nslookup car.com

![nslookup command output](images/day2-nslookup.png)

Result:

Server: UnKnown
Address: 192.168.x.x (local network resolver, masked for privacy)
Non-authoritative answer:
Name: car.com
Address: 150.171.109.2

Analysis: The initial Server/Address pair reflects the local resolver used for the query, in this case the home router, not the target domain. The final Name/Address pair confirms the actual resolved IP address for car.com. The response was marked non-authoritative, indicating it was served by an intermediate resolver rather than car.com's own authoritative name servers directly, which is standard behavior for typical DNS queries.


4. PRACTICAL EXERCISE: NETWORK PATH TRACING

Command executed: tracert car.com

![tracert command output](images/day2-tracert.png)

Result summary: The trace identified 11 responsive hops before subsequent hops ceased responding through to the maximum hop limit of 30.

Hop 1: Local router (private network address, masked), 2-3ms
Hops 2-3, 5: Internal ISP network addresses (private range, masked), 33-67ms
Hops 4, 6-8: Non-responsive (common behavior for routers configured to ignore diagnostic probes)
Hop 9: Public internet exit point (102.89.59.6), 54-81ms
Hop 10: Microsoft network infrastructure, Lagos hub (104.44.196.177), 54-81ms
Hop 11: Microsoft network infrastructure, Cape Town hub (104.44.42.24), 110-126ms
Hops 12-30: Non-responsive, consistent with Azure's internal network routing typically not responding to traceroute probes for security reasons

Analysis: The trace confirms traffic exits the local ISP network and enters Microsoft's global network backbone, corroborating the Azure hosting finding from Day 1's header analysis. The Lagos and Cape Town hostnames identify Microsoft's own network infrastructure locations along the routing path, not necessarily the physical location of car.com's origin server. Azure Front Door's distributed edge architecture likely directs traffic to the nearest available edge node before internal Azure routing takes over, which explains why the trace does not reach a final identifiable endpoint.

Note: Private/internal IP addresses (192.168.x.x and 10.x.x.x ranges) observed during this exercise have been masked in this report. These addresses are not publicly routable and are internal to the local network and ISP infrastructure, but are withheld as standard practice to avoid disclosing internal network topology in a public document.


5. DNS RESOLUTION PATH DIAGRAM

Your Computer
    -> Local Router / DNS Resolver (private address, masked)
    -> ISP Recursive DNS Resolver (non-authoritative)
    -> Root DNS Servers
    -> .com TLD Servers
    -> car.com Authoritative Name Servers
    -> Resolved IP Address: 150.171.109.2
    -> Browser initiates TCP connection to resolved IP


6. NETWORK PATH DIAGRAM

Your Computer
    -> Home Router (private address, masked)
    -> ISP Internal Network (private range, masked)
    -> [Silent hops - internal ISP routing]
    -> Public Internet Exit Point (102.89.59.6)
    -> Microsoft Network, Lagos (104.44.196.177)
    -> Microsoft Network, Cape Town (104.44.42.24)
    -> [Silent hops - Azure internal routing]
    -> car.com content served via Azure Front Door


7. KEY TAKEAWAY

DNS resolution and network path tracing are two independent techniques that, when used together, cross-validate infrastructure findings. The DNS lookup confirmed the resolved IP address for car.com, while the traceroute confirmed the network path passes through Microsoft's global backbone, directly corroborating the Azure hosting and Azure Front Door findings identified through response header analysis on Day 1. This demonstrates the value of combining multiple reconnaissance techniques rather than relying on a single data source.


8. VULNERABILITIES IDENTIFIED

None. This session remained scoped to passive DNS resolution and standard network diagnostic tooling. No active scanning, enumeration, or intrusive testing was performed against car.com or any third-party system.


9. ETHICS AND LEGALITY STATEMENT

All activity documented in this report constitutes standard, non-intrusive network diagnostics equivalent to normal DNS resolution and path tracing performed automatically by any device connecting to a website. No active scanning, enumeration, brute-forcing, or intrusive testing was performed against car.com or any third-party system.


10. STATUS

DNS resolution theory covered - Done
TCP/IP model theory covered - Done
nslookup exercise completed - Done
tracert exercise completed - Done
DNS resolution path diagrammed - Done
Day 2 objectives met - Done
Proceeding to Day 3, Intercepting Traffic with Burp Suite - Pending


Documented as part of a 20-week Cybersecurity and Web Security Analyst training program.