# NodeLocal DNSCache Helm chart

This chart deploys NodeLocal DNSCache Daemon set according to https://kubernetes.io/docs/tasks/administer-cluster/nodelocaldns/.

It is designed to work both with iptables and IPVS setup.

## Setting upstream DNS service

```
config:
  localDnsIp: 169.254.20.11
  zones:
    - zone: .:53
      plugins:
        errors: true
        reload: true
        debug: false
        log: true
        cache:
          parameters: 30
          denial: {}
            # size: 0
            # ttl: 1
          success: {}
            # size: 8192
            # ttl: 30
          prefetch: {}
            # amount: 1
            # duration: 10m
            # percentage: 20%
          serve_stale: false
        forward:
          parameters: __PILLAR__UPSTREAM__SERVERS__ # defaults to /etc/resolv.conf
          force_tcp: false
          prefer_udp: false
          policy: "" # random|round_robin|sequential
          max_fails: "" # 10
          expire: "" # 10s
          health_check: "" # 10s
        prometheus: true
        health:
          port: 8080
```

```
  Corefile: |
    __PILLAR__DNS__DOMAIN__:53 {
        errors
        cache {
          success 9984 30
          denial 9984 5
        }
        reload
        loop
        bind __PILLAR__LOCAL__DNS__ __PILLAR__DNS__SERVER__
        forward . __PILLAR__CLUSTER__DNS__ {
          force_tcp
        }
        prometheus :9253
        health __PILLAR__LOCAL__DNS__:8080
    }
    in-addr.arpa:53 {
        errors
        cache 30
        reload
        loop
        bind __PILLAR__LOCAL__DNS__ __PILLAR__DNS__SERVER__
        forward . __PILLAR__CLUSTER__DNS__ {
          force_tcp
        }
        prometheus :9253
    }
    ip6.arpa:53 {
        errors
        cache 30
        reload
        loop
        bind __PILLAR__LOCAL__DNS__ __PILLAR__DNS__SERVER__
        forward . __PILLAR__CLUSTER__DNS__ {
          force_tcp
        }
        prometheus :9253
    }
    .:53 {
        errors
        cache 30
        reload
        loop
        bind __PILLAR__LOCAL__DNS__ __PILLAR__DNS__SERVER__
        forward . __PILLAR__UPSTREAM__SERVERS__
        prometheus :9253
    }
```

https://github.com/kubernetes/kubernetes/blob/master/cluster/addons/dns/nodelocaldns/nodelocaldns.yaml