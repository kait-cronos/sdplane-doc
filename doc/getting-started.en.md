[Top](README.en.md) > Getting Started

# Getting Started

**Language:** **English** | [日本語](getting-started.ja.md)

This guide covers the steps from installation to running sdplane-oss.

## 1. Install Dependencies

See [Install Dependencies Guide](manual/en/install-dependencies.md) for detailed instructions on installing all required dependencies including liburcu-qsbr, libpcap, lthread, and DPDK.

## 2. Install From Pre-compiled Debian Package

For Intel Core i3-n305/Celeron j3160, quick installation is possible with Debian packages.

Download and install the pre-built Debian package:

```bash
# Download the latest package for n305
wget https://www.yasuhironet.net/download/n305/sdplane_0.1.4-36_amd64.deb
wget https://www.yasuhironet.net/download/n305/sdplane-dbgsym_0.1.4-36_amd64.ddeb

# or for j3160
wget https://www.yasuhironet.net/download/j3160/sdplane_0.1.4-35_amd64.deb
wget https://www.yasuhironet.net/download/j3160/sdplane-dbgsym_0.1.4-35_amd64.ddeb

# Install the package
sudo apt install ./sdplane_0.1.4-*_amd64.deb
sudo apt install ./sdplane-dbgsym_0.1.4-*_amd64.ddeb
```

**Note**: Check [yasuhironet.net downloads](https://www.yasuhironet.net/download/) for the latest package version.

**Note**: Use of this pre-compiled binary on other CPUs may cause SIGILL (Illegal Instruction). In that case you have to compile by yourself.

Jump to 5. System Configuration.

## 3. Build and Install From Source

See [Build and Install from Source Guide](manual/en/build-install-source.md) for detailed instructions on building sdplane-oss from source code.

You can jump to 5. System Configuration.

## 4. Build Debian Package and Install

See [Build Debian Package Guide](manual/en/build-debian-package.md) for instructions on creating and installing a Debian package from source.

## 5. System Configuration

See [System Configuration Guide](manual/en/system-configuration.md) for instructions on configuring hugepages, network interfaces, and optional kernel modules.

## 6. Sdplane Configuration

### Configuration Files

When installed from Debian Package, `/etc/sdplane/sdplane.conf.sample` and systemd service files are automatically generated.

Create `/etc/sdplane/sdplane.conf` referring to the samples.

#### OS Configuration Examples (`etc/`)
- [`etc/sdplane.conf.sample`](../etc/sdplane.conf.sample): Main configuration template
- [`etc/sdplane.service`](../etc/sdplane.service): systemd service file
- [`etc/modules-load.d/`](../etc/modules-load.d/): Kernel module loading configuration

#### Application Configuration Examples (`example-config/`)
- [`example-config/sdplane-pktgen.conf`](../example-config/sdplane-pktgen.conf): Packet generator configuration
- [`example-config/sdplane-topton.conf`](../example-config/sdplane-topton.conf): Topton hardware configuration
- [`example-config/sdplane_l2_repeater.conf`](../example-config/sdplane_l2_repeater.conf): L2 repeater configuration
- [`example-config/sdplane_enhanced_repeater.conf`](../example-config/sdplane_enhanced_repeater.conf): Enhanced repeater configuration with VLAN switching, router interfaces, and capture interfaces

## 7. Run the Sdplane Application

```bash
# Run in foreground
sudo sdplane

# Run with config file
sudo sdplane -f /etc/sdplane/sdplane_enhanced_repeater.conf

# When installed via apt, run via systemd
sudo systemctl enable sdplane
sudo systemctl start sdplane

# Connect to CLI
telnet localhost 9882
```
