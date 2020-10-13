# Installation [Beta]

## Minimum Requirements

Kubernetes cluster must have the following minimum requirements available:

|CPU Unit|RAM |Disk Space|Processor Type|
|--------|--- |----------|--------------|
|1       |2 GB|10 GB     |64 Bit        |

## DB-less mode(Recommended)

To install Gluu Gateway on kuberentes, follow these steps:

1. [Pre-requirement] Install Gluu on [kubernetes](https://gluu.org/docs/gluu-server/4.2/installation-guide/install-kubernetes/) if not already installed and make sure OXD server is installed by answering `Y` to `Install Casa`. Answer `N` to prompt `Install Gluu Gateway Database mode` as this option is for Database mode. 

1. Create namespace `kong` for Gluu-Gateway

    ```bash
    kubectl create ns kong
    ```

1. Create `kong.yml` declarative configuration before proceeding. Head to [DB-less](db-less-setup.md) Cloud Native edition section and finish steps there, then continue here. Please note that loading `kong.yml` occurs automatically as the `kong.yml` gets pulled from secrets and loaded if changes occur to it. An update on `kong.yml` requires only an update to the secret created in the next step. The deployment will automatically re-load upon changes. 


1. Once done with creating `kong.yml` create secret called `kong-config`  in the same namespace as Gluu Gateway

    ```bash
    kubectl create secret generic kong-config -n kong --from-file=kong.yml
    ```
    
        
1. Install Kong with GG plugins. The only component that must be changed inside kongs manifests is the `image:tag` of kong to `gluufederation/gluu-gateway:4.2.1_03`. Please refer to [kongs](https://docs.konghq.com/latest/kong-for-kubernetes/install) kubernetes installation for more tweaks and detail.
    
    === "YAML"
    
        ```bash
           wget https://bit.ly/kong-ingress-dbless && cat kong-ingress-dbless | sed -s "s@image: kong:2.1@image: gluufederation/gluu-gateway:4.2.1_03@g" | kubectl apply -f -
        ```
        
    === "Helm"
    
        Please refer to kongs official [chart](https://github.com/Kong/charts/tree/master/charts/kong) for more options.
 
        ```bash
           helm repo add kong https://charts.konghq.com
           helm repo update
           # Helm 3
           helm install gg-kong kong/kong --set ingressController.installCRDs=false --set image.repository=gluufederation/gluu-gateway --set image.tag=4.2.1_03 --set --namespace=kong
        ```
        
1. In order for the kong deployment to read the secret containing `kong.yml` you must grant it permissions by adding the `get` to to the `CluserRole` of kong handling the `secrets` resource.

    ```bash
    kubectl edit ClusterRole kong-ingress-clusterrole
    
    apiVersion: rbac.authorization.k8s.io/v1beta1
    kind: ClusterRole
    metadata:
      name: kong-ingress-clusterrole
    rules:
    - apiGroups:
      - ""
      resources:
      - endpoints
      - nodes
      - pods
      - secrets
      verbs:
      - list
      - watch
      - get # <---- Add this here
    ```

Head to [DB-less](db-less-setup.md) for more information. Please note that loading `kong.yml` occurs automatically as the `kong.yml` gets pulled from secrets and loaded if changes occur to it.

### Uninstall

```bash
   helm delete gg-kong -n gluu-gateway
```

## DB mode

!!! Warning
    Installing Gluu Gateway ui automatically adds an ingress and allows access through the loadbalancer for Gluu deployment. This is a security risk and it is better to use `kubectl port-forward <gg-ui-pod> 443:8443 -n <gluu-namespace>` to access the UI when needed. You can delete the ingress definition using `kubectl delete <gg-ui-ingress-name> -n <gluu-namespace>`. 
    
!!! Note
    Please make sure that the Kubernetes cluster is `1.16.x`. Higher versions has an issue with the current postgres operator setup.

## Pre-requisites

- Please install [Helm v3]("https://helm.sh/docs/intro/install/") which is required for KubeDB in both methods of installation below.

### Automatic full installation

1. Install Gluu with Gluu gateway on [kubernetes](https://gluu.org/docs/gluu-server/4.2/installation-guide/install-kubernetes/).
  
     ```bash
        ./pygluu-kubernetes.pyz install
        # To install Gluu-Gateway only: Gluu must be already installed in the same kubernetes cluster
        ./pygluu-kubernetes.pyz install-gg-dbmode
     ```

### Helm

1. Install Gluu and Postgres run. Answer `Y` to prompt `Install Gluu Gateway`. This will install Gluu, Postgres, and setup the databases and required secrets for Gluu-Gateway and Gluu-Gateway-UI.

    ```bash
       ./pygluu-kubernetes.pyz helm-install
    ```

1. Wait until Gluu is up and running. Make sure oxd server and oxauth pods are running

    ```bash
        kubectl -n gluu wait --for=condition=available --timeout=900s deploy/gluu-oxauth
        kubectl -n gluu wait --for=condition=available --timeout=300s deploy/gluu-oxd-server
    ```
    
1. Install Gluu Gateway and Gluu Gateway UI

    ```bash
        ./pygluu-kubernetes.pyz helm-install-gg-dbmode
    ``` 

### Uninstall

```bash
    ./pygluu-kubernetes.pyz uninstall-gg-dbmode
    # With helm
    ./pygluu-kubernetes.pyz helm-uninstall-gg-dbmode
```
