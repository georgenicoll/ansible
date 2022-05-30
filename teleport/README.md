# Teleport Scripts

## Setup teleport on all rPIs

Run the following to install teleport and set it up to connect to the teleport server on each nodes.  NOTE: before running, login to teleport using `tsh login`:

```
ansible-playbook teleport.node.setup.yaml
```

to remove, run `teleport.node.uninstall.yaml`.

## Installing Teleport into a k8 cluster running on arm64

At the time of writing (May 2022) there is no arm64 image of teleport.  The following instructions will build one, push to a remote repo 
and run the helm install command to use that built image.  Substitute the image `georgenicoll/teleport` as required.

### Build arm64 image

1. clone https://github.com/gravitational/teleport to an rPi
1. checkout the version required: `git checkout v${VERSION}`
1. Build and upload  image (from the teleport directory):
    
    * Configure Environment and Build:
        ```
        export ARCH=arm64
        export DOCKER_IMAGE=georgenicoll/teleport
        export DOCKER_IMAGE_CI=georgenicoll/teleport-ci
        make full
        ```
        wait for a *long* time.

    * Now create the docker image:
        ```
        mkdir docker-tmp
        cd docker-tmp
        cp ../build/* .
        cp ../build.assets/charts/Dockerfile .
        ##!! Update the copied DockerFile to inherit FROM ubuntu:22.04 (or whichever is currently running on the Pi)
        docker build --tag georgenicoll/teleport:${VERSION} .
        docker tag georgenicoll/teleport:$VERSION georgenicoll/teleport:latest
        ```
    * Finally upload the image to docker hub:
        ```
        docker push georgenicoll/teleport:${VERSION}
        docker push georgenicoll/teleport:latest
        ```

### Set up teleport in the cluster

1. From the teleport UI, go to Kubernetes->Add Kubernetes
1. Run the command to add the helm repo, if necessary
1. Fill in the `Namespace` and `Cluster Name`
1. Click on the `Generate Script`
1. Copy the generated file locally (the following assumes `cluster-values.yaml`)
1. Append the following to the `cluster-values.yaml`:
    ```
    image: georgenicoll/teleport
    ```
1. Run the helm command (adjust from the one given in the UI):
    ```
    helm install teleport-agent teleport/teleport-kube-agent -f cluster-values.yaml --create-namespace --namespace teleport
    ```
### Set up the access role with the correct kubernetes_groups

For some reason when first set up, anything I tried with kubectl was giving me an error much like the following:
```
error: the server doesn't have a resource type "pods"
```
To get around this, the teleport access role needed the additional group `system:masters` added to it (from the [teleport kubernetes demo](https://youtu.be/2diX_UAmJ1c)):

1. In the Web UI navigate to `Team -> Roles`
1. Click on `Options -> Edit` next to the `access` group
1. Add the group `system:masters` to the `kubernetes_groups` section
1. Log back in using `tsh kube login <clustername>`

This should all now work.

