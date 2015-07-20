# netflix-proxy
`Docker` packaged smart DNS proxy to watch `Netflix`, `Hulu`[n2], `HBO Now` and others out of region using `BIND` and `sniproxy`[n1].

# Supported Services
The following are supported out of the box, however adding additional services is trivial and is done by editing `zones.override` and `sniproxy.conf` files:
* Netflix
* Hulu
* HBO Now 
* Amazon Instant Video
* Crackle
* Pandora
* Vudu
* bbc.co.uk

# Instructions
The following paragraphs show how to get this solution up and running with a few different Cloud providers I've tried so far.

## DigitalOcean
The following is based on a standard Ubuntu Docker image provided by `DigitalOcean`, but should in theory work on any Linux distribution **with** Docker pre-installed.

1. Head over to [Digital Ocean](https://www.digitalocean.com/?refcode=937b01397c94) to get $10 USD credit to create a Docker VM
2. Create a `Droplet` using `Docker 1.x` on `Ubuntu 14.04` (find in under Applications images).
3. Make sure you create the `Droplet` in the right location, for example if you want to watch US content, create in the US.
3. SSH to your `Droplet` and run the following command..
4. `cd /opt && git clone https://github.com/ab77/netflix-proxy.git && cd netflix-proxy && ./build.sh`
5. Point your DNS at the Droplet IP and watch `Netflix`, `Hulu` and `HBO Now` out of region.
6. Enjoy or raise a new [issue](https://github.com/LordZepto/netflix-proxy/issues/new) if something doesn't work quite right..

### Security
The build script automatically configures the system with **DNS recursion turned on**. This has security implications, since it potentially opens your DNS server to a DNS amplification attack, a kind of a [DDoS attack](https://en.wikipedia.org/wiki/Denial-of-service_attack). This should not be a concern however, as long as the `iptables` firewall rules configured automatically by the build script for you remain in place. However if you ever decide to turn the firewall off, please be aware of this.

If you want to turn DNS recursion off, please be aware that you will need a mechanism to selectively send DNS requests for domains your DNS server knows about (i.e. netflix.com) to your VPS and send all of the other DNS traffic to your local ISP's DNS server. Something like [Dnsmasq](http://www.thekelleys.org.uk/dnsmasq/doc.html) can be used for this and some Internet routers even have it built in. In order to switch DNS recursion off, you will need to build your system using the following command:

`cd /opt && git clone https://github.com/LordZepto/netflix-proxy.git && cd netflix-proxy && ./build.sh -r 0 -b 1`

### Command Line Options
The following command line options can be optionaly passed to `build.sh` for additional control:

    Usage: ./build.sh [-r 0|1] [-b 0|1] [-c <ip>]
        -r      enable (1) or disable (0) DNS recursion (default: 1)
        -b      grab docker images from repository (0) or build locally (1) (default: 0)
        -c      specify client-ip instead of being taken from ssh_connection[n3]

## Other Cloud Providers

### Linode
The following is based on a standard Ubuntu image provided by `Linode`, but should work on any Linux distribution **without** Docker installed.

1. Head over to [Linode](https://www.linode.com/?r=ceb35af7bad520f1e2f4232b3b4d49136dcfe9d9) and sign-up for an account.
2. Create a new `Linode` and deploy an `Ubuntu 14-04 LTS` image into it.
3. Make sure you create the Linode in the right location, as there a few to pick from.
3. SSH to your `Linode` and run the following command..
4. `curl -sSL https://get.docker.com/ | sh && cd /opt && git clone https://github.com/ab77/netflix-proxy.git && cd netflix-proxy && ./build.sh`
5. Point your DNS at the `Linode` IP and watch `Netflix`, `Hulu` and/or `HBO Now` out of region.
6. Binge. Not that there is anything wrong with that or raise a new [issue](https://github.com/ab77/netflix-proxy/issues/new) if something doesn't work quite right..

### DreamCompute by DreamHost
The following is based on a standard Ubuntu image provided by `DreamHost`, but should work on any Linux distribution **without** Docker installed and running under **non-root** user.

1. Head over to [DreamHost]( http://www.dreamhost.com/r.cgi?2124700) and sign-up for an account.
2. Find the `DreamCompute` or `Public Cloud Computing` section and launch an `Ubuntu 14-04-Trusty` instance.
3. Make sure to add an additional firewall rule to allow DNS: `Ingress	IPv4	UDP	53	0.0.0.0/0 (CIDR)`
4. Also add a `Floating IP` to your instance, otherwise it will only have an IPv6 IP.
3. SSH to your instance and run the following command..
4. `curl -sSL https://get.docker.com/ | sh && sudo usermod -aG docker $(who am i | awk '{print $1}') && cd /opt && sudo git clone https://github.com/ab77/netflix-proxy.git && cd netflix-proxy && ./build.sh`
5. Point your DNS at the instance IP and watch `Netflix`, `Hulu` and/or `HBO Now` out of region.
6. Well done, enjoy or raise a new [issue](https://github.com/ab77/netflix-proxy/issues/new) if something doesn't work quite right..

-- [ab1](https://plus.google.com/+AntonBelodedenko?rel=author)

[n1] https://github.com/dlundquist/sniproxy by Dustin Lundquist dustin@null-ptr.net

[n2] At the time of writing (May 2015), `Hulu` appears to be geo-restricted from `DigitalOcean` and `Linode` US IPs, but working from `DreamCompute` IAD DC IPs (at least the ones I've tried).

[n3] You can now specify your home/office/etc. IP manually using `-c <ip>` option to `build.sh`.
