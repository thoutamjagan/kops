# Description of Keys in `config` and `cluster.spec`

This list is not complete, but aims to document any keys that are less than self-explanatory.

## spec


### api

This object configures how we expose the API:

* `dns` will allow direct access to master instances, and configure DNS to point directly to the master nodes.
* `loadBalancer` will configure a load balancer (ELB) in front of the master nodes, and configure DNS to point to the ELB.

DNS example:

```yaml
spec:
  api:
    dns: {}
```


When configuring a LoadBalancer, you can also choose to have a public ELB or an internal (VPC only) ELB.  The `type`
field should be `Public` or `Internal`.

```yaml
spec:
  api:
    loadBalancer:
      type: Public
```

### sshAccess

This array configures the CIDRs that are able to ssh into nodes. On AWS this is manifested as inbound security group rules on the `nodes` and `master` security groups.

Use this key to restrict cluster access to an office ip address range, for example.

```yaml
spec:
  sshAccess:
    - 12.34.56.78/32
```

### apiAccess

This array configures the CIDRs that are able to access the kubernetes API. On AWS this is manifested as inbound security group rules on the ELB or master security groups.

Use this key to restrict cluster access to an office ip address range, for example.

```yaml
spec:
  apiAccess:
    - 12.34.56.78/32
```

### kubeAPIServer

This block contains configuration for the `kube-apiserver`.

#### oidc flags for Open ID Connect Tokens

Read more about this here: https://kubernetes.io/docs/admin/authentication/#openid-connect-tokens

```yaml
spec:
  kubeAPIServer:
    oidcIssuerURL: https://your-oidc-provider.svc.cluster.local
    oidcClientID: kubernetes
    oidcUsernameClaim: sub
    oidcGroupsClaim: user_roles
    oidcCAFile: /etc/kubernetes/ssl/kc-ca.pem
```

#### runtimeConfig

Keys and values here are translated into `--runtime-config` values for `kube-apiserver`, separated by commas.

Use this to enable alpha features, for example:

```yaml
spec:
  kubeAPIServer:
    runtimeConfig:
      batch/v2alpha1: "true"
      apps/v1alpha1: "true"
```

Will result in the flag `--runtime-config=batch/v2alpha1=true,apps/v1alpha1=true`. Note that `kube-apiserver` accepts `true` as a value for switch-like flags.

### networkID

On AWS, this is the id of the VPC the cluster is created in. If creating a cluster from scratch, this field doesn't need to be specified at create time; `kops` will create a `VPC` for you.

```yaml
spec:
  networkID: vpc-abcdefg1
```