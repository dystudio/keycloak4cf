# Keycloak Cluster for Cloud Foundry

> [Keycloak](https://www.keycloak.org/about.html) is an open source Identity and Access Management solution aimed at modern applications and services. It makes it easy to secure applications and services with little to no code.

The purpose of this repo is to run the Keycloak Cluster on [Cloud Foundry](https://www.cloudfoundry.org/).
Kyecloak using Infinispan to cache: realms, users, sessions and more. If you want to run more then one Keycloak CF app instance you have to run it in [Standalone Clustered Mode](https://www.keycloak.org/docs/latest/server_installation/index.html#_standalone-ha-mode).

## Quick Start

1. Make sure that you can use (is enabled by CF operator): [CF internal routing](https://docs.cloudfoundry.org/devguide/deploy-apps/routes-domains.html#internal-routes), [CF network policy](https://docs.cloudfoundry.org/devguide/deploy-apps/cf-networking.html), [CF diego docker](https://docs.cloudfoundry.org/adminguide/docker.html#enable).
2. Modify (domains, database access, passwords) and use this application manifest [`manifest.yml`](cf-manifests/manifest.yml)
3. Push the app `cf push`
4. Add network policy: `cf add-network-policy keycloak-poc --destination-app keycloak-poc --protocol tcp --port 7600`
5. Restart the app `cf restart keycloak-poc`

Now you can login to Keycloak using admin web console (get initial admin password from your manifest).

You can check the cluster using `jboss-cli`:

- `cf ssh keycloak-poc -i 0` (any instance)
- `/opt/jboss/keycloak/bin/jboss-cli.sh -c`
- `/subsystem=jgroups/channel=ee:read-attribute(name=view)`

The result should contains as many instances as you declare:
```json
{
"outcome" => "success",
"result" => "[70d3d988-fb5a-4ceb-406c-6d04|1] (2) [70d3d988-fb5a-4ceb-406c-6d04, 14a493d4-3a48-4323-6287-7c56]"
}
``` 

If your cluster doesn't work please go to the [Debugging](#debugging) section.

## Under the Hood

This work is based on [Official Keycloak Docker Image](https://hub.docker.com/r/jboss/keycloak/).

Clustering is done by:

- using [Infinispan](http://infinispan.org)/JGroups [DNS_PING protocol](http://jgroups.org/manual4/index.html#_dns_ping)
- [internal routing](https://docs.cloudfoundry.org/devguide/deploy-apps/routes-domains.html#internal-routes) and [BOSH DNS](https://bosh.io/docs/dns/) 
- [switch clustering](https://kb.novaordis.com/index.php/WildFly_Clustering_without_Multicast) to TCP instead of multicast UDP (there is no multicast on Cloud Foundry) 


## Keycloak Docker Repos

- [Official Keycloak Docker Image](https://hub.docker.com/r/jboss/keycloak/)
- [Keycloak for CF](docker/keycloak4cf)
- [Keycloak for CF + debug tools](docker/keycloak4cf-debug) based on [Keycloak + debug tools](docker/keycloak-docker-debug)

## CF Manifests

### Production

For security (no root access) and performance (limited logs) use this application manifest [`manifest.yml`](cf-manifests/manifest.yml) in production.
It is based on this docker image `i6ecloud/keycloak4cf:latest`.

### Debugging

If you want to debug use this application manifest [`manifest-debug.yml`](cf-manifests/manifest-debug.yml) based on this docker image `i6ecloud/keycloak4cf-debug:latest`.

Now you can:
- get super user rights: `sudo su`, password for jboss user is now `jboss`
- use tcpdump and analize the result using Wireshark: `tcpdump -s 0  -i eth0 -w /tmp/tcpdump.pcap`
- restart Keycloak: `/opt/jboss/keycloak/bin/jboss-cli.sh  --connect command=:reload`
- check DNS entry: `host keycloak-poc-debug.apps.internal`
- check network configuration: `ip a`
- netcat: `nc -tvn  10.255.244.233 7600`
- check debug logs: `tail -f /opt/jboss/keycloak/standalone/log/*.log`
- check the cluster: `/opt/jboss/keycloak/bin/jboss-cli.sh -c` and then `/subsystem=jgroups/channel=ee:read-attribute(name=view)`

