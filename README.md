# osTicketLab
## Skills Demonstrated

- Linux server administration (Ubuntu) via SSH — package management, file permissions, service management
- Building a full LAMP stack (Linux, Apache, MySQL, PHP) from a bare VM
- Secure MySQL setup: dedicated least-privilege database user rather than root/admin credentials
- Deploying and hardening a real web application (removing the setup installer, locking down config file permissions post-install)
- Help desk platform administration: agents, teams/groups, departments, help topic routing
- Service Level Agreement (SLA) configuration and understanding of response/resolution targets
- End-to-end ticket lifecycle management from both the requester and agent perspective
- Azure fundamentals: resource groups, VM provisioning, inbound port/firewall rules, cost control (deallocating/deleting resources when finished)

## Build Process

1. **Provisioned the VM** — Created `rg-osticket-lab` and deployed `tix-vm01` (Ubuntu Server 24.04 LTS, Standard_B2s) with inbound rules for SSH (22) and HTTP (80).
2. **Installed the LAMP stack** — Updated packages, then installed Apache2, MySQL, and PHP with the extensions osTicket requires; ran `mysql_secure_installation` to harden the database server.
3. **Created the database** — Created the `osticket` database and a dedicated `osticketuser` account scoped only to that database (`GRANT ALL PRIVILEGES ON osticket.*`), rather than using root for the application.
4. **Deployed osTicket** — Downloaded the latest release, copied the application files into Apache's web root (`/var/www/html`), created `ost-config.php` from the sample config, and set correct `www-data` ownership/permissions.
5. **Ran the web installer** — Completed the `/setup/` wizard in-browser: helpdesk name and system email, admin account, and database connection settings.
6. **Locked down the install** — Deleted the `/setup/` directory and reset `ost-config.php` back to read-only permissions once installation succeeded, closing the post-install security gap.
7. **Configured the help desk** — Built out the operational structure through the staff control panel (`/scp/`):
   - 2–3 agent accounts representing support staff
   - A support team with agents assigned to it
   - Multiple departments (e.g., IT Support, Facilities) to route requests by function
   - Multiple help topics (e.g., Password Reset, Hardware Issue, Software Install Request), each routed to the correct department
   - An SLA plan (response/resolution targets) assigned to a help topic
8. **Validated the system end-to-end** — Submitted sample tickets from the public-facing portal using different help topics and priority levels, then worked them from the staff panel: assigned to an agent, added internal notes, replied to the requester, and moved each through Open → In Progress → Resolved/Closed. One ticket was deliberately left past its SLA target to see a breach from the agent's side.

## What Was Configured

- **Agents:** Multiple support staff accounts
- **Teams:** At least one support team with agents assigned
- **Departments:** Multiple departments reflecting how requests would be split up in a real organization
- **Help Topics:** Multiple topics, each routed to the correct department
- **SLA Plan:** One plan defining response and resolution targets, assigned to a help topic
- **Tickets:** Multiple sample tickets submitted and carried through the full lifecycle (new → assigned → in progress → resolved/closed), including one intentional SLA breach scenario

## Security Notes

- The application database uses a dedicated, least-privilege MySQL user rather than the root account.
- The `/setup/` installer directory was removed immediately after installation to prevent reinstallation by an outside party.
- The configuration file's write permissions were reverted to read-only once setup completed.
- SSH and HTTP are the only inbound ports opened on the VM's network security group.

## Screenshots

_Add screenshots here to round out the portfolio:_
- Ticket queue showing multiple open tickets
- A full ticket conversation thread (agent reply + internal note)
- Departments / Help Topics / SLA configuration screens

## Cleanup

The VM was stopped ("Stopped (deallocated)") in the Azure Portal when not actively in use, and the `rg-osticket-lab` resource group is deleted once the project is fully documented, to ensure the lab stops billing entirely.

## Related Projects

- [Active Directory Domain Services Lab](https://github.com/AndrewJohnson-1/ActiveDirectoryLab) — companion Azure homelab covering Windows Server, Active Directory, and domain-joined client administration.
