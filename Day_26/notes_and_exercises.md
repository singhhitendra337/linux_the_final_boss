# Day 26: Mega Project – End-to-End DevOps/Linux Challenge

## Project Overview
Welcome to the capstone challenge! This project simulates a real-world scenario where you must set up, secure, automate, and troubleshoot a Linux-based environment for a web application, following best practices from the entire curriculum.

## Scenario
You are hired as a DevOps Engineer for a startup. Your task is to:
- Provision a Linux server (VM or cloud)
- Set up users, groups, and permissions
- Install and configure Apache or Nginx to serve a static website
- Secure the server (firewall, SSH, updates, hardening)
- Automate backups and monitoring
- Document and script all steps for reproducibility

## Requirements
- Use only the command line (no GUI tools)
- All configurations and scripts must be version-controlled (e.g., Git)
- Provide clear documentation (README)
- Use automation wherever possible (shell scripts, cron, etc.)

## Step-by-Step Tasks
1. **Provisioning:**
   - Launch a Linux VM (locally or in the cloud)
   - Update the system and set the hostname
2. **User & Group Management:**
   - Create users for dev, ops, and admin roles
   - Set up groups and assign users appropriately
   - Enforce password policies and SSH key authentication
3. **Web Server Setup:**
   - Install Apache or Nginx
   - Configure a virtual host/server block for a static website
   - Set correct permissions for web content
   - Enable and test HTTPS (self-signed or Let's Encrypt)
4. **Security Hardening:**
   - Configure the firewall to allow only necessary ports
   - Disable root SSH login and enforce key-based auth
   - Set up fail2ban or similar for brute-force protection
   - Regularly update and audit the system
5. **Automation & Backups:**
   - Write scripts to automate backups of web content and configs
   - Schedule backups with cron
   - Set up monitoring for CPU, memory, disk, and web server logs
6. **Documentation:**
   - Create a README with setup steps, scripts, and troubleshooting tips
   - Document all customizations and security measures
7. **Testing & Troubleshooting:**
   - Simulate failures (e.g., service crash, full disk) and recover
   - Restore from backup and verify integrity

## Best Practices
- Use least privilege for all users and services
- Keep all software up to date
- Use version control for scripts and configs
- Test all automation in a staging environment first
- Regularly review logs and monitor key metrics

## Deliverables
- All scripts, configs, and documentation in a Git repository
- A working, secure, and automated Linux web server
- A summary report of lessons learned and challenges faced

## Reflection & Interview-Style Questions
1. What were the biggest challenges you faced and how did you overcome them?
2. How did you ensure the security and reliability of your setup?
3. What would you automate further if given more time?
4. How would you scale this setup for high availability?
5. How did you document and share your work for team collaboration?

---

Congratulations on completing the Mega Project and the entire Linux curriculum! This project brings together all your skills and prepares you for real-world DevOps and SRE roles.
