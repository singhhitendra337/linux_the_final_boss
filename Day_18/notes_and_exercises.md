# Day 18: Web Servers (Apache, Nginx, Reverse Proxy)

## Notes
- **Why Web Servers Matter:**
  - Serve web content, APIs, and enable application delivery.
  - Core skill for DevOps, SRE, and cloud engineers.

- **Apache HTTP Server:**
  - Install: `apt install apache2` or `yum install httpd`
  - Start/stop: `systemctl start|stop apache2` or `httpd`
  - Config: `/etc/apache2/` (Debian), `/etc/httpd/` (RHEL)
  - Main config: `apache2.conf` or `httpd.conf`
  - Virtual hosts: `/etc/apache2/sites-available/`
  - Enable site: `a2ensite site.conf`; reload: `systemctl reload apache2`
  - Logs: `/var/log/apache2/`

- **Nginx:**
  - Install: `apt install nginx` or `yum install nginx`
  - Start/stop: `systemctl start|stop nginx`
  - Config: `/etc/nginx/`, main: `nginx.conf`
  - Server blocks: `/etc/nginx/sites-available/`
  - Test config: `nginx -t`; reload: `systemctl reload nginx`
  - Logs: `/var/log/nginx/`

- **Reverse Proxy:**
  - Nginx/Apache can proxy requests to backend servers
  - Example Nginx block:
    ```
    location /api/ {
      proxy_pass http://localhost:5000/;
    }
    ```
  - Use for load balancing, SSL termination, caching

- **Best Practices:**
  - Use least privilege for web server user
  - Keep software updated
  - Use HTTPS (Let's Encrypt, Certbot)
  - Restrict access to admin interfaces
  - Monitor logs for suspicious activity

- **Diagram:**
  - ![Web Server Diagram](https://upload.wikimedia.org/wikipedia/commons/7/7e/Reverse_proxy.png)

## In-Depth Explanations & Scenarios
- **Serving Static vs. Dynamic Content:**
  - Static: HTML, CSS, images; Dynamic: PHP, Python, Node.js via FastCGI, WSGI, etc.
- **Load Balancing:**
  - Distribute traffic across multiple backend servers for high availability
- **SSL/TLS:**
  - Use Certbot to automate certificate management
- **Real-World Example:**
  - Set up Nginx as a reverse proxy for a Node.js app, enable HTTPS, and monitor logs

## Sample Exercises
1. Install and start Apache and Nginx on your system.
2. Configure a virtual host in Apache and a server block in Nginx.
3. Set up Nginx as a reverse proxy for a backend service.
4. Enable HTTPS using Let's Encrypt.
5. Analyze web server logs for errors and suspicious activity.

## Solutions
1. `apt install apache2 nginx`; `systemctl start apache2 nginx`
2. Edit `/etc/apache2/sites-available/your-site.conf` and `/etc/nginx/sites-available/your-site`; enable and reload
3. Edit Nginx config with `proxy_pass`; reload Nginx
4. `apt install certbot python3-certbot-nginx`; `certbot --nginx`
5. `grep error /var/log/nginx/error.log`; `grep 404 /var/log/apache2/access.log`

## Sample Interview Questions
1. What is the difference between Apache and Nginx?
2. How do you configure a virtual host in Apache? A server block in Nginx?
3. What is a reverse proxy and why use it?
4. How do you enable HTTPS on a web server?
5. How do you troubleshoot a web server that won't start?
6. How do you restrict access to admin interfaces?
7. How do you monitor and analyze web server logs?
8. What are the security best practices for web servers?
9. How do you set up load balancing with Nginx?
10. How do you serve static and dynamic content?

## Interview Question Answers
1. Apache uses process/thread model, Nginx is event-driven and better for high concurrency.
2. Apache: edit `sites-available` and enable; Nginx: edit `sites-available` and link to `sites-enabled`.
3. A reverse proxy forwards client requests to backend servers for load balancing, SSL, or caching.
4. Use Certbot or manually install SSL certificates and configure server blocks/virtual hosts.
5. Check config syntax, logs, and port conflicts; use `nginx -t` or `apachectl configtest`.
6. Restrict by IP, use firewalls, or password-protect admin areas.
7. Use `grep`, `awk`, or log analysis tools on access/error logs.
8. Run as non-root, keep updated, use HTTPS, restrict access, monitor logs.
9. Use `upstream` blocks in Nginx to define backend servers.
10. Static: serve files directly; Dynamic: use FastCGI, WSGI, or proxy to app servers.
