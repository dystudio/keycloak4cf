# Keycloak Cluster for Cloud Foundry

> [Keycloak](https://www.keycloak.org/about.html) is an open source Identity and Access Management solution aimed at modern applications and services. It makes it easy to secure applications and services with little to no code.

The purpose of this repo is to run the Keycloak Cluster on [Cloud Foundry](https://www.cloudfoundry.org/).
Kyecloak using Infinispan to cache: realms, users, sessions and more. If you want to run more then one Keycloak CF app instance (HA mode) you have to run it in [Standalone Clustered Mode](https://www.keycloak.org/docs/latest/server_installation/index.html#_standalone-ha-mode).


**Docker Repos**:

- Official Keycloak Docker Image: [`jboss/keycloak`](https://hub.docker.com/r/jboss/keycloak/)
- Keycloak for CF: [`i6ecloud/keycloak4cf`](https://cloud.docker.com/u/i6ecloud/repository/docker/i6ecloud/keycloak4cf)
- Keycloak for CF incl. debug tools: [`i6ecloud/keycloak4cf-debug`](https://cloud.docker.com/u/i6ecloud/repository/docker/i6ecloud/keycloak4cf-debug)
  - based on Keycloak incl. debug tools: [`i6ecloud/keycloak-debug`](https://cloud.docker.com/u/i6ecloud/repository/docker/i6ecloud/keycloak-debug)

[Read more...](https://github.com/i6e/keycloak4cf/blob/master/README-full.md)