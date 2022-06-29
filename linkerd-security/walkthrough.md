# Installing Linkerd with custom certificates
This is all about installing Linkerd with a custom CA or cert-manager

## Steps

### Create a cert
* Requires `[step](https://smallstep.com/cli/)`
step certificate create identity.linkerd.cluster.local ca.crt ca.key --profile root-ca --no-password --insecure
step certificate create identity.linkerd.cluster.local issuer.crt issuer.key --ca ca.crt --ca-key ca.key --profile intermediate-ca --not-after 8760h --no-password --insecure

### Cluster
#### AKS
#### AWS
#### GKE

### Install an app
`kubectl create ns ingress-nginx`
`kubectl apply -f ingress`
`kubectl apply -f emojivoto/emojivoto.yml`

### Install linkerd
* From here: https://linkerd.io/2/tasks/generate-certificates/
`linkerd install --identity-issuance-lifetime=12h0m0s --identity-issuer-certificate-file certs/issuer.crt --identity-issuer-key-file certs/issuer.key --identity-trust-anchors-file certs/ca.crt | kubectl apply -f -`
`linkerd dashboard &`

### Inject app namespace and restart app deployments
`kubectl annotate ns emojivoto linkerd.io/inject=enabled`
`kubectl annotate ns ingress-nginx linkerd.io/inject=enabled`
`kubectl rollout restart deploy -n emojivoto`
`kubectl rollout restart deploy -n ingress-nginx`
`linkerd stat deploy -n emojivoto`
`linkerd tap <pod> -n emojivoto`
`linkerd tap <pod> -n emojivoto --to=deploy/books`

### Apply service profiles
`kubectl apply -f emojivoto/sp`
`linkerd routes deploy -n emojivoto`

### Split Traffic
`kubectl apply -f module2/web-svc.yml`
`kubectl apply -f module2/web-svc-oc.yml`
`kubectl apply -f module2/traffic-split.yml`
`linkerd stat ts -n emojivoto`


### distributed tracing


