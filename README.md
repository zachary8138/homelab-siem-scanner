# Homelab Wazuh Orchestrator

This PPA provides two explicit Wazuh deployment utilities for Ubuntu 24.04.

Installing either package does not deploy Wazuh automatically, modify UFW
defaults, alter SSH access, or enable the firewall.

## Manager-only package

Recommended for lightweight home-lab servers.

    sudo add-apt-repository ppa:rist138/ppa
    sudo apt update
    sudo apt install homelab-siem-scanner
    sudo homelab-siem-setup

This installs Wazuh Manager without Wazuh Indexer or Dashboard. Alerts remain
available locally under `/var/ossec/logs/alerts/`.

Optional tools:

    sudo homelab-siem-setup --with-clamav --with-nmap

Optional agent firewall rules:

    sudo homelab-siem-setup --configure-ufw 192.168.1.0/24

Only ports 1514/tcp and 1515/tcp are added. The utility does not change UFW
defaults, SSH access, or firewall activation state.

## Full Wazuh package

The optional full package installs Wazuh Manager, Indexer, and Dashboard:

    sudo apt install homelab-siem-scanner-full
    sudo homelab-siem-full-setup

Recommended minimum resources are 4 vCPUs, 8 GiB RAM, and 50 GiB storage.

Optional firewall rules:

    sudo homelab-siem-full-setup --configure-ufw 192.168.1.0/24

## Upgrade behavior

Upgrading either PPA package never automatically removes an existing Wazuh
deployment or firewall rule. Existing installations must be migrated or removed
explicitly.

## Logs

Manager setup:

    /var/log/homelab-siem-setup.log

Full-stack setup:

    /var/log/homelab-siem-full-setup.log

## License

GPL-3. See `debian/copyright`.
