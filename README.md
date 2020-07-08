# NodeLocal DNSCache Helm chart

This chart deploys NodeLocal DNSCache Daemon set according to https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/.

It is designed to work both with iptables and IPVS setup.

## Important notes

### iptables

When using iptables, set the following variables:

```
config:
  dnsSvcIp: 100.64.255.31

# Only when you want to use CoreDNS upstream instead of default kube-dns
service:
  upstreamSelector:
    k8s-app: coredns
```

### IPVS

When using IPVS, you need to leave `config.dnsSvcIp` blank and set `config.nodeLocalSvcIp` to the IP address of the upstream DNS service.

```
config:
  dnsSvcIp:
  # Upstream CoreDNS IP address
  nodeLocalSvcIp: 100.64.0.10
```