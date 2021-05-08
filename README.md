# Subspace - A simple WireGuard VPN server GUI

Fork for usage with Reverse proxy and Custom Nameserver

* **WireGuard VPN Protocol**
  * The most modern and fastest VPN protocol.
* **Single Sign-On (SSO) with SAML**
  * Support for SAML providers like G Suite and Okta.
* **Add Devices**
  * Connect from Mac OS X, Windows, Linux, Android, or iOS.
* **Remove Devices**
  * Removes client key and disconnects client.
* **Auto-generated Configs**
  * Each client gets a unique downloadable config file.
  * Generates a QR code for easy importing on iOS and Android.

## Run Subspace on a VPS

Running Subspace on a VPS is designed to be as simple as possible.

  * Public Docker image.
  * Single static Go binary with assets bundled.
  * Works with a reverse proxy or standalone.

**Recommended Specs**

* Type: VPS or dedicated
* Distribution: Ubuntu 16.04 (Xenial)
* Memory: 512MB or greater



**Requirements**

* Your server must be reachable over the internet on port 51820/udp (WireGuard).

### Usage

**Example usage:**

```bash
$ subspace --http-host subspace.example.com
```
### Usage

```bash
  -backlink string
        backlink (optional)
  -datadir string
        data dir (default "/data")
  -debug
        debug mode
  -help
        display help and exit
  -http-addr string
        HTTP listen address (default ":80")
  -http-host string
        HTTP host
  -http-insecure
        enable sessions cookies for http (no https) not recommended
  -letsencrypt
        enable TLS using Let's Encrypt on port 443 (default true)
  -version
        display version and exit
```
### Run as a Docker container

#### Install WireGuard on the host

The container expects WireGuard to be installed on the host. The official image is `subspacecloud/subspace`.

```bash
add-apt-repository -y ppa:wireguard/wireguard
apt-get update
apt-get install -y wireguard

# Remove dnsmasq because it will run inside the container.
apt-get remove -y dnsmasq

# Load modules.
modprobe wireguard
modprobe iptable_nat
modprobe ip6table_nat

# Enable IP forwarding
sysctl -w net.ipv4.ip_forward=1
sysctl -w net.ipv6.conf.all.forwarding=1

```

Follow the official Docker install instructions: [Get Docker CE for Ubuntu](https://docs.docker.com/engine/installation/linux/docker-ce/ubuntu/)

Make sure to change the `--env SUBSPACE_HTTP_HOST` to your domain nameand `--env NAMESERVER` to your preffered Nameserver.

```bash

# Your data directory should be bind-mounted as `/data` inside the container using the `--volume` flag.
$ mkdir /data

docker create \
    --name subspace \
    --restart always \
    --network host \
    --cap-add NET_ADMIN \
    --volume /usr/bin/wg:/usr/bin/wg \
    --volume /data:/data \
    --env SUBSPACE_HTTP_HOST=subspace.example.com \
    --env NAMESERVER=NAMESERVER IP
    latschenharry/subspace:latest

$ sudo docker start subspace

$ sudo docker logs subspace

<log output>

```

#### Updating the container image

Pull the latest image, remove the container, and re-create the container as explained above.

```bash
# Pull the latest image
$ sudo docker pull latschenharry/subspace

# Stop the container
$ sudo docker stop subspace

# Remove the container (data is stored on the mounted volume)
$ sudo docker rm subspace

# Re-create and start the container
$ sudo docker create ... (see above)
```

## Help / Reporting Bugs

Not (currently) available. Coming soon.

