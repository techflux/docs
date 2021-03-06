---
title: Chart Options
weight: 276
---

### Common Options

| Option | Default Value | Description |
| --- | --- | --- |
| `hostname` | " " | `string` - the Fully Qualified Domain Name for your Rancher Server |
| `ingress.tls.source` | "rancher" | `string` - Where to get the cert for the ingress. - "rancher, letsEncrypt, secret" |
| `letsEncrypt.email` | " " | `string` - Your email address |
| `letsEncrypt.environment` | "production" | `string` - Valid options: "staging, production" |
| `privateCA` | false | `bool` - Set to true if your cert is signed by a private CA |

<br/>

### Advanced Options

| Option | Default Value | Description |
| --- | --- | --- |
| `auditLog.level` | 0 | `int` - set the [API Audit Log]({{< baseurl >}}/rancher/v2.x/en/installation/api-auditing) level. 0 is off. [0-3] |
| `debug` | false | `bool` - set debug flag on rancher server |
| `imagePullSecrets` | [] | `list` - list of names of Secret resource containing private registry credentials |
| `proxy` | "" | `string` - string - HTTP[S] proxy server for Rancher |
| `noProxy` | "localhost,127.0.0.1" | `string` - comma separated list of hostnames or ip address not to use the proxy |
| `resources` | {} | `map` - rancher pod resource requests & limits |
| `rancherImage` | "rancher/rancher" | `string` - rancher image source |
| `rancherImageTag` | same as chart version | `string` - rancher/rancher image tag |
| `tls` | "ingress" | `string` - Where to terminate SSL. - "ingress, external" |

<br/>

### API Audit Log

Enabling the [API Audit Log](https://rancher.com/docs/rancher/v2.x/en/installation/api-auditing/) will create a sidecar container in the Rancher pod. This container (`rancher-audit-log`) will stream the log to `stdout`.

You can collect this log as you would any container log. Enable the [Logging service under Rancher Tools](https://rancher.com/docs/rancher/v2.x/en/tools/logging/) for the `System` Project on the Rancher server cluster.

```
--set auditLog.level=1
```

### HTTP Proxy

Rancher requires internet access for some functionality (helm charts). Use `proxy` to set your proxy server.

Add your IP exceptions to the `noProxy` list. Make sure you add the Service cluster IP range (default: 10.43.0.1/16) and any worker cluster `controlplane` nodes. Rancher supports CIDR notation ranges in this list.

```
--set proxy="http://<username>:<password>@<proxy_url>:<proxy_port>/"
--set noProxy="127.0.0.1,localhost,10.0.0.0/8,172.16.0.0/12,192.168.0.0/16"
```

### Private Registry and Air Gap Installs

See [Installing Rancher - Air Gap]({{< baseurl >}}/rancher/v2.x/en/installation/air-gap-installation/install-rancher/) for details on installing Rancher with a private registry.

### External TLS Termination

If you wish to terminate the SSL/TLS on a load-balancer external to the Rancher cluster (ingress), use the `--tls=external` option and point your load balancer at port http 80 on all of the rancher cluster nodes.

> **Note:** If you are using a Private CA signed cert, add `--set privateCA=true` and see [Adding TLS Secrets - Private CA Signed - Additional Steps]({{< baseurl >}}/rancher/v2.x/en/installation/ha/helm-rancher/tls-secrets/#private-ca-signed---additional-steps) to add the CA cert for Rancher.

Your load balancer must support long lived websocket connections and will need to insert proxy headers so Rancher can route links correctly.

> **Note:** The `tls=external` option will expose the Rancher interface on http port 80.  Clients that are allowed to connect directly to the Rancher cluster will not be encrypted. We recommend that you restrict direct access at the network level to just your load balancer.

#### Required headers

* `Host`
* `X-Forwarded-Proto`
* `X-Forwarded-Port`
* `X-Forwarded-For`

#### Health checks

Rancher will respond `200` to health checks on the `/healthz` endpoint.
