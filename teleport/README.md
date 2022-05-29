# Installing Teleport into a k8 cluster running on arm64

## Build arm64 image

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

## Set up teleport in the cluster

1. From the teleport UI, go to Kubernetes->Add Kubernetes
1. Run the command to add the helm repo, if necessary
1. Fill in the `Namespace` and `Cluster Name`
1. Click on the `Generate Script`
1. Copy the generated file locally (following assumes to `cluster-values.yaml`)
1. Append the following to the `cluster-values.yaml`:
    ```
    image: georgenicoll/teleport
    ```
1. Run the helm command (adjust from the one given in the UI):
    ```
    helm install teleport-agent teleport/teleport-kube-agent -f cluster-values.yaml --create-namespace --namespace teleport
    ```

