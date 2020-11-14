# Gogs for OpenShift
Gogs is the Go Git service. Learn more about it at https://gogs.io/

Running containers on OpenShift comes with certain security and other
requirements. This repository contains:

* A Dockerfile for building an OpenShift-compatible Gogs image
* Various scripts used in the Docker image
* OpenShift templates for deploying the image
* Usage instructions
## Deployment
There are two templates available: _persistent_ and _non-persistent_. The pesistent one requires two `PersistentVolume` available with default size required of 1Gi (the volume size can be specified with the template variables: `GOGS_VOLUME_CAPACITY` and `DB_VOLUME_CAPACITY`).

If you want to configure Gogs to use `https` instead of `http` pass the variable `PROTOCOL` with value `https`, then update the route to do tls like edge
```yaml
spec:
  host: ${HOSTNAME}
  to:
    name: gogs
  tls:
    termination: edge
    insecureEdgeTerminationPolicy: Allow
```

* gogs-data
* gogs-postgres-data

Both templates will provision two linked pods: one for GOGS and other for Postgresql DB. If your have persistent volumes available in your cluster:

```
oc new-app -f https://raw.githubusercontent.com/csantanapr/gogs/master/gogs-template-ephemeral.yaml --param=HOSTNAME=gogs-tools.$(oc get ingresses.config.openshift.io cluster -o template={{.spec.domain}}) -n tools
```

Otherwise:
```
oc new-app -f https://raw.githubusercontent.com/csantanapr/gogs/master/gogs-template.yaml --param=HOSTNAME=gogs-tools.$(oc get ingresses.config.openshift.io cluster -o template={{.spec.domain}}) -n tools
```

Note that hostname is required during Gogs installation in order to configure repository urls correctly.

## Gogs Versions
You can deploy any of the available Gogs versions on [quay.io/siamaksade/gogs](https://quay.io/repository/siamaksade/gogs?tab=tags) on Quay using the ```GOGS_VERSION``` template parameter:
```
oc new-app -f https://raw.githubusercontent.com/csantanapr/gogs/master/gogs-template-ephemeral.yaml --param=HOSTNAME=gogs-demo.yourdomain.com --param=GOGS_VERSION=0.11.4
```

# Gogs Admin User
After Gogs deployment, the first registered user will be admin. The default administrator can log into Admin > Users and authorize another user. A user will also be an > administrator if they register in the install page. Read more on [Gogs FAQ](https://gogs.io/docs/intro/faqs#how-can-i-become-an-administrator%3F)



