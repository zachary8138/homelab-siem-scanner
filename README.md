# Homelab Wazuh Orchestrator

A lightweight Ubuntu PPA providing explicit setup utilities for deploying Wazuh in home-lab environments.

Nothing is deployed automatically when the packages are installed. The utilities do not change UFW defaults, modify SSH access, enable the firewall, or assume a particular VPN or network interface.

## Available packages

### Manager-only deployment

`homelab-siem-scanner` installs a setup utility for Wazuh Manager without the resource-intensive Wazuh Indexer or Dashboard.

Recommended minimum:

- Ubuntu 24.04 LTS
- 64-bit AMD64 or ARM64
- 2 CPU cores
- Approximately 2 GB RAM

This option has no web dashboard. Alerts are stored locally under:

```text
/var/ossec/logs/alerts/
```

### Full Wazuh deployment

`homelab-siem-scanner-full` provides an optional all-in-one deployment containing:

- Wazuh Manager
- Wazuh Indexer
- Wazuh Dashboard

The Wazuh Indexer is OpenSearch-based; this package does not install the legacy Elastic ELK stack.

Recommended minimum:

- Ubuntu 24.04 LTS
- 4 vCPUs
- 8 GiB RAM
- 50 GiB storage

The two packages conflict intentionally and cannot be installed simultaneously.

## Add the PPA

```bash
sudo add-apt-repository ppa:rist138/ppa
sudo apt update
```

## Manager-only installation

```bash
sudo apt install homelab-siem-scanner
sudo homelab-siem-setup
```

Available options:

```text
--configure-ufw CIDR
--with-clamav
--with-nmap
--keep-repository-enabled
--help
```

Install optional scanning tools:

```bash
sudo homelab-siem-setup --with-clamav --with-nmap
```

Allow Wazuh agents from a specific network:

```bash
sudo homelab-siem-setup --configure-ufw 192.168.1.0/24
```

This adds only:

- `1514/tcp` for agent communication
- `1515/tcp` for agent enrollment

It does not alter SSH rules, change UFW’s default policy, or enable UFW.

## Full installation

```bash
sudo apt install homelab-siem-scanner-full
sudo homelab-siem-full-setup
```

Available options:

```text
--configure-ufw CIDR
--wazuh-series VERSION
--keep-repository-enabled
--force
--help
```

Example with restricted dashboard and agent access:

```bash
sudo homelab-siem-full-setup \
  --configure-ufw 192.168.1.0/24
```

The full installer refuses systems below its recommended hardware threshold unless `--force` is explicitly supplied.

## Safety behavior

Package installation itself performs no network deployment or global firewall changes.

The setup utilities:

- Require explicit root execution.
- Download only from official Wazuh HTTPS endpoints.
- Use Wazuh’s signed APT repository for manager installation.
- Reject unrestricted UFW sources such as `0.0.0.0/0`.
- Do not modify SSH access.
- Do not enable UFW.
- Do not change UFW default policies.
- Disable the Wazuh repository after installation by default to prevent unplanned component upgrades.

Use `--keep-repository-enabled` if repository lifecycle is managed separately.

## Upgrade behavior

Upgrading either orchestrator package does not automatically:

- Remove an existing Wazuh deployment.
- Remove Wazuh data.
- Remove existing firewall rules.
- Convert a manager-only deployment into a full deployment.
- Convert a full deployment into manager-only mode.

Migration and removal are intentionally explicit operations.

## Logs

Manager-only setup:

```text
/var/log/homelab-siem-setup.log
```

Full setup:

```text
/var/log/homelab-siem-full-setup.log
```

Full-installation artifacts and credentials are stored with root-only permissions under:

```text
/var/lib/homelab-siem-scanner-full/
```

## Removing the orchestrator

Removing the PPA package removes only its setup utility:

```bash
sudo apt remove homelab-siem-scanner
```

or:

```bash
sudo apt remove homelab-siem-scanner-full
```

It does not automatically uninstall Wazuh or delete security data.

## Intended use

This project is intended for personal laboratories, training environments, and small test networks. Review Wazuh’s official architecture and sizing guidance before production use.

## References

- [Wazuh documentation](https://documentation.wazuh.com/)
- [Wazuh quickstart](https://documentation.wazuh.com/current/quickstart.html)
- [Launchpad PPA](https://launchpad.net/~rist138/+archive/ubuntu/ppa)

## License

Distributed under GPL-3. See `debian/copyright` for licensing details.
