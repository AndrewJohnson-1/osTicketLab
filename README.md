<p align="center">
<img width="654" height="268" alt="image" src="https://github.com/user-attachments/assets/b676675c-a7c7-48a1-afaa-2954b7df78ae" />

# osTicket Help Desk Ticketing System — Azure Homelab

Self-hosted help desk system, built on an Ubuntu VM in Azure and configured the way a small support team would actually run it — not just installed, but set up with departments, SLAs, and real ticket handling.

## Overview

This project installs [osTicket](https://osticket.com/) on an Ubuntu Server VM and sets it up the way a real support team would: agents, a team, departments, help topics routed to the right department, and an SLA plan. After that, sample tickets went through the public portal and got worked from the staff side — assigned, replied to, and moved through Open, In Progress, and Resolved/Closed.

The point was to see both halves of the tool: what it takes to stand it up and lock it down, and what it's actually like to work a ticket queue as an agent.

## Environment

| Component | Detail |
|---|---|
| Cloud platform | Microsoft Azure |
| Resource group | `rg-osticket-lab` |
| VM | `tix-vm01` — Standard_B2s |
| OS | Ubuntu Server 24.04 LTS |
| Web server | Apache2 |
| Database | MySQL |
| Language runtime | PHP (+ mbstring, xml, gd, imap, intl, apcu extensions) |
| Application | osTicket (latest release) |
| Remote access | SSH (port 22) for server administration |
| Web access | HTTP (port 80) for the ticketing portal and staff panel |

**Architecture:**
<img width="2240" height="1319" alt="image" src="https://github.com/user-attachments/assets/62dee800-f466-4c32-9652-c57ddcf09b48" />

## Skills Demonstrated

- Linux server administration (Ubuntu) via SSH — package management, file permissions, service management
- Building a LAMP stack (Linux, Apache, MySQL, PHP) from a bare VM
- Secure MySQL setup: a dedicated, least-privilege database user instead of root
- Deploying and hardening a real web application — removing the setup installer, locking config file permissions back down after install
- Help desk platform administration: agents, teams, departments, help topic routing
- SLA configuration and what a response/resolution target actually means in practice
- Ticket lifecycle management, start to finish, from both the requester and agent side
- Azure fundamentals: resource groups, VM provisioning, inbound port rules, cost control (deallocating/deleting resources when done)

## Build Process

1. **Provisioned the VM.** Created `rg-osticket-lab` and deployed `tix-vm01` — Ubuntu Server 24.04 LTS, Standard_B2s — with SSH (22) and HTTP (80) opened on the network security group.
2. **Built the LAMP stack.** Updated packages, then installed Apache2, MySQL, and PHP with the extensions osTicket needs. Ran `mysql_secure_installation` before touching anything else.
3. **Created the database and a dedicated user.** `osticket` database, `osticketuser` scoped to it with `GRANT ALL PRIVILEGES ON osticket.*` — not root, so a compromised app account still can't touch anything outside its own database.
4. **Deployed osTicket.** Downloaded the latest release, copied it into `/var/www/html`, built `ost-config.php` from the sample file, and set `www-data` ownership so Apache could actually serve it.
5. **Ran the web installer.** Helpdesk name, system email, admin account, database connection — filled in through the `/setup/` wizard in a browser.
<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/d19e584d-815a-4bc7-ae99-920765c53606" />

6. **Locked the install down.** Deleted `/setup/` and reset `ost-config.php` back to 0644 the moment the installer finished, since leaving either one in place is the real security hole.
<img width="1204" height="174" alt="image" src="https://github.com/user-attachments/assets/20ab3da4-9e1c-4eca-8130-340bb8625a4d" />

7. **Set up the help desk like a real team would run one**, through the `/scp/` staff panel: a couple of agent accounts, a support team, two departments (IT Support, Facilities), three help topics routed to the right department, and an SLA plan attached to one of them.
<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/ed2a3b03-a0ee-4360-93b0-65bef7578bd8" />
<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/d93fccc8-ddab-42f7-bf1f-e8720acabacf" />
<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/01dcbbe6-e770-426e-8e4b-1657e249458e" />


8. **Worked it like a queue.** Submitted tickets from the public portal with different topics and priorities, then handled them from the staff side — assigned, internal notes, replies, moved through Open → In Progress → Resolved/Closed. Left one sitting past its SLA on purpose to see what a breach actually looks like from the agent's seat.
<img width="2880" height="1800" alt="image" src="https://github.com/user-attachments/assets/564530bc-f50d-4434-9a34-b5b611910c5a" />
<img width="2846" height="4620" alt="image" src="https://github.com/user-attachments/assets/edf0bf37-325e-4021-ad92-0a9430ed247f" />


## What Was Configured

- **Agents:** Multiple support staff accounts
- **Teams:** One support team with agents assigned
- **Departments:** IT Support and Facilities
- **Help Topics:** Multiple topics, each routed to a department
- **SLA Plan:** Response and resolution targets, assigned to a help topic
- **Tickets:** Several sample tickets carried through the full lifecycle, including one intentional SLA breach

## Security Notes

- The application connects to MySQL with a dedicated, least-privilege user — not root.
- `/setup/` was deleted right after installation so it couldn't be used to reinstall over the live system.
- `ost-config.php` went back to read-only permissions once setup finished.
- SSH and HTTP are the only inbound ports open on the VM.

## Cleanup

Stopped `tix-vm01` ("Stopped (deallocated)") in the Azure Portal whenever it wasn't actively in use, and deleted `rg-osticket-lab` once this was documented, so nothing kept billing in the background.

## Related Projects

- [Active Directory Domain Services Lab](https://github.com/AndrewJohnson-1/ActiveDirectoryLab) — companion Azure homelab covering Windows Server, Active Directory, and domain-joined client administration.
