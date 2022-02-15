
# ns-apps

* Check the [GitOps Repo](https://github.com/RedHat-EMEA-SSA-Team/ns-gitops/tree/single-app) to deploy this app with GitOps.

## Application set

cat <<EOF | oc apply -f -
---
apiVersion: argoproj.io/v1alpha1
kind: ApplicationSet
metadata:
  name: ns-apps
  namespace: openshift-gitops
spec:
  generators:
  - git:
      repoURL: https://github.com/danifernandezs/ns-apps.git
      revision: git-generator
      files:
       # incluir  en el path de overlays
      - path: "cluster-config/**/**/config.json"
  template:
    metadata:
      name: '{{cluster.name}}-{{env}}'
    spec:
      project: default
      source:
        repoURL: https://github.com/romerobu/applicationset.git
        targetRevision: git-generator
        path: "apps/bgd/overlays/{{env}}"
      destination:
        server: '{{cluster.address}}'     
EOF   