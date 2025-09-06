# -Foundational-Cloud-Lab-TLS-Load-Balancing-Routing
A basic web app hosted on private servers, exposed via an internet-facing load balancer with TLS termination.
[Client Browser] ⇄ [HTTPS] ⇄ [Load Balancer] ⇄ [HTTP] ⇄ [Private Web Servers]


 Components
- Cloud Provider: AWS Free Tier / Local VMs with HAProxy/Nginx
- Domain: Use a free domain from Freenom or subdomain from DuckDNS
- TLS Cert: Generate with Let's Encrypt using Certbot
- Web Servers: 2 private instances running a basic HTTP server (e.g., Python Flask or Nginx)
- Load Balancer: AWS ALB / HAProxy / Nginx reverse proxy


🔐 1. Identifying TLS/SSL Files
When setting up HTTPS, you typically deal with three key files

Server certificate (.crt / .pem): Issued by a CA, proves the website’s identity.

Private key (.key): Secret key that stays only on the server, used for decryption/signing.

CA chain (bundle): Intermediate + root certificates that browsers trust to verify the server cert.

👉 Example:

server.crt → goes on the LB/web server


🌐 2. Applying a Certificate to a Load Balancer
For an internet-facing load balancer (e.g., AWS ALB):
- Upload the server certificate and private key (or reference an ACM cert in AWS)
- Configure HTTPS listener on port 443
- Set up TLS termination at the LB so traffic to backends is plain HTTP (unless end-to-end encryption is needed)
This allows the LB to handle encryption/decryption, reducing load on backend servers.

🧭 3. Mapping Domain to Load Balancer & Routing to Private Web Servers

Steps:
- DNS Mapping: Point your domain (e.g., app.example.com) to the LB’s public DNS name or IP using an A or CNAME record.
- Routing: Configure LB rules to forward traffic to backend targets (private IPs or instances in a private subnet).
- Security: Ensure backend servers only accept traffic from the LB (via security groups or firewall rules).
This setup keeps your web servers isolated from the internet while still serving public traffic.

🚫 4. Why Backends Don’t Need Public IPs with an Internet-Facing LB
- The load balancer acts as the public entry point.
- It forwards requests to private IPs inside your VPC/subnet.
- This improves security: backends are not exposed to the internet, reducing attack surface.
- You can use NAT gateways if backends need outbound internet access.
Think of the LB as a secure receptionist—only it talks to the outside world.


🔹 5. Simple HTTP headers & status codes

301 vs 302:

301 → Permanent redirect

302 → Temporary redirect

401 vs 403:

401 → Unauthorized (needs login/credentials)

403 → Forbidden (you’re authenticated but not allowed)

429 → Too many requests (rate-limiting / throttling)

5xx → Server errors (500 = generic, 502 = bad gateway, 503 = service unavailable, 504 = gateway timeout)

server.key → must never leave the server

ca-bundle.crt → installed alongside the server cert
