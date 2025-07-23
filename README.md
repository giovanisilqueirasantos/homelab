# Personal Homelab

Welcome to my personal homelab repository. This project serves as a playground for experimenting with various technologies, automating workflows, and self-hosting applications. It’s a space where I can learn, build, and refine my skills in a real-world environment.

This repository contains configurations and documentation for my homelab setup, focusing on automation, self-hosting, and infrastructure as code. The goal is to create a scalable, secure, and maintainable environment that mirrors production-grade systems.

---

## Architecture & Infrastructure

- **Kubernetes Orchestration**  
  Lightweight Kubernetes cluster managed with k3s, optimized for homelab use.

- **Provisioning & GitOps**  
  Continuous deployment and infrastructure provisioning managed with FluxCD, enabling GitOps workflows.

- **Secrets Management**  
  Secrets encrypted and managed securely using [SOPS](https://github.com/mozilla/sops), integrated with GitOps for safe secret handling in version control.

- **Networking**  
  - VLANs and firewall rules to simulate secure enterprise networking.  
  - Cloudflare Tunnel for secure, easy remote access to internal applications without exposing your home IP.

- **Storage & Databases**  
  - NAS-based persistent storage integrated into Kubernetes for reliable data storage.  
  - PostgreSQL databases deployed and managed with [CloudNativePG](https://cloudnative-pg.io/) for scalable and resilient data management.

- **Backup & Disaster Recovery**  
  Regular backups of data and databases stored securely on [Cloudflare R2](https://developers.cloudflare.com/r2/), ensuring redundancy and easy recovery.

---

## Applications & Services

The homelab hosts a variety of services, including:

- **Media & Entertainment:**  
  - [Jellyfin](https://jellyfin.org/) — Personal media server for streaming movies, TV shows, and music.  
  - [Audiobookshelf](https://github.com/advplyr/audiobookshelf) — Self-hosted audiobook management and streaming.

- **Productivity & Security:**  
  - [Linkding](https://github.com/sissbruecker/linkding) — Self-hosted bookmark manager.  
  - [Passbolt](https://www.passbolt.com/) — Open-source password manager for teams.

- Monitoring & logging with Prometheus, Grafana, and ELK.

- Automated workflows using custom scripts and Kubernetes operators.

---

## Security & Access

- VPN for secure remote access.  
- Cloudflare Tunnel to securely expose applications to the internet without opening firewall ports.  
- Strict firewall policies.  
- TLS/SSL encryption for services.  
- Backup and disaster recovery plans leveraging Cloudflare R2.

---

## Inspiration & Invitation

Feel free to **copy, adapt, and build your own homelab** based on this repository. Whether you’re learning, experimenting, or creating a production-ready setup, this repo can serve as a solid foundation.
