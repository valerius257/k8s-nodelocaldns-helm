# NodeLocal DNSCache Helm chart

This chart deploys NodeLocal DNSCache Daemon set according to https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/.

It is designed to work both with iptables and IPVS setup.

## Setting upstream DNS service

```
config:
  upstreamDns: 100.64.255.31
```