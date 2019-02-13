# Dragonfly Quick Start

In this quick start guide, you will get a feeling of Dragonfly by starting a [SuperNode](overview/terminology.md) (the server) in your Docker container, installing the Dragonfly client (the client), and then downloading a container image and a general file, which are likely what you'll be doing frequently in your use case.

## Prerequisites

You have started your Docker container.

## Step 1: Starting a SuperNode (the Server) in Your Docker Container

1. Pull the docker image we provided.

    ```bash
    # Replace ${imageName} with the real image name
    docker pull ${imageName}
    ```

    **Note:** Choose one of the images we provide according to your geo-location, and replace `${imageName}` with it:

    - China: `registry.cn-hangzhou.aliyuncs.com/dragonflyoss/supernode:0.2.1`
    - US: `registry.us-west-1.aliyuncs.com/dragonflyoss/supernode:0.2.1`

2. Start a SuperNode.

    ```bash
    # Replace ${imageName} with the real image name
    docker run -d -p 8001:8001 -p 8002:8002 ${imageName}
    ```

For example, if you're in China, run the following commands:

```bash
docker pull registry.cn-hangzhou.aliyuncs.com/dragonflyoss/supernode:0.2.1

docker run -d -p 8001:8001 -p 8002:8002 registry.cn-hangzhou.aliyuncs.com/dragonflyoss/supernode:0.2.1
```

## Step 2: Installing Dragonfly Client

You have two options of installing Dragonfly client: installing from source code, or installing by pulling the image.

### Option 1: Installing from Source Code

1. Download a package of the client.

    ```bash
    cd $HOME
    # Replace ${package} with a package appropriate for your operating system and location
    wget ${package}
    ```

    **Note:** Choose one of the packages we provide according to your geo-location, and replace `${package}` with it:

    - If you're in China:

        - [Linux 64-bit](http://dragonflyoss.oss-cn-hangzhou.aliyuncs.com/df-client_0.2.1_linux_amd64.tar.gz): `http://dragonflyoss.oss-cn-hangzhou.aliyuncs.com/df-client_0.2.1_linux_amd64.tar.gz`

        - [MacOS 64-bit](http://dragonflyoss.oss-cn-hangzhou.aliyuncs.com/df-client_0.2.1_darwin_amd64.tar.gz): `http://dragonflyoss.oss-cn-hangzhou.aliyuncs.com/df-client_0.2.1_darwin_amd64.tar.gz`

    - If you're not in China:

        - [Linux 64-bit](https://github.com/dragonflyoss/Dragonfly/releases/download/v0.2.1/df-client_0.2.1_linux_amd64.tar.gz): `https://github.com/dragonflyoss/Dragonfly/releases/download/v0.2.1/df-client_0.2.1_linux_amd64.tar.gz`

        - [MacOS 64-bit](https://github.com/dragonflyoss/Dragonfly/releases/download/v0.2.1/df-client_0.2.1_darwin_amd64.tar.gz): `https://github.com/dragonflyoss/Dragonfly/releases/download/v0.2.1/df-client_0.2.1_darwin_amd64.tar.gz`

2. Unzip the package.

    ```bash
    # Replace ${package} with a package appropriate for your operating system and location
    tar -zxf ${package}
    ```

3. Add the directory of `df-client` to your `PATH` environment variable to make sure you can directly use `dfget` and `dfdaemon` command.

    ```bash
    # Execute or add this line to ~/.bashrc
    export PATH=$PATH:$HOME/df-client/
    ```

For example, if you're in China and using Linux, run the following commands:

```bash
cd $HOME
wget http://dragonflyoss.oss-cn-hangzhou.aliyuncs.com/df-client_0.2.1_linux_amd64.tar.gz
tar -zxf df-client_0.2.1_linux_amd64.tar.gz
# execute or add this line to ~/.bashrc
export PATH=$PATH:$HOME/df-client/
```

### Option 2: Installing by Pulling the Image

1. Pull the docker image we provided.

    ```bash
    docker pull dragonflyoss/dfclient:v0.3.0_dev
    ```

2. Start dfdaemon.

    ```bash
    docker run -d -p 65001:65001 dragonflyoss/dfclient:v0.3.0_dev --registry https://xxx.xx.x
    ```

3. Configure the Daemon Mirror.

    a. Modify the configuration file `/etc/docker/daemon.json`.

    ```sh
    vi /etc/docker/daemon.json
    ```

    **Tip:** For more information on `/etc/docker/daemon.json`, see [Docker documentation](https://docs.docker.com/registry/recipes/mirror/#configure-the-cache).

    b. Add or update the configuration item `registry-mirrors` in the configuration file.

    ```sh
    "registry-mirrors": ["http://127.0.0.1:65001"]
    ```

    c. Restart Docker daemon.

    ```bash
    systemctl restart docker
    ```

## Step 3: Downloading Images or Files

Now that you have started your SuperNode, and installed Dragonfly client, you can start downloading images or general files, both of which are supported by Dragonfly, but with slightly different downloading methods.

### Use Case 1: Downloading a General File with Dragonfly

Once you have installed the Dragonfly client, you can use the `dfget` command to download a file.

```bash
dfget -u 'https://github.com/dragonflyoss/Dragonfly/blob/master/docs/images/logo.png' -o /tmp/logo.png
```

**Tip:** For more information on the dfget command, see [dfget](cli_ref/dfget.md).

### Use Case 2: Pulling an Image with Dragonfly

1. Start `dfdaemon` with a specified registry, such as `https://index.docker.io`.

    ```bash
    nohup dfdaemon --registry https://index.docker.io > /dev/null 2>&1 &
    ```

2. Add the following line to the dockerd configuration file [/etc/docker/daemon.json](https://docs.docker.com/registry/recipes/mirror/#configure-the-docker-daemon).

    ```json
    "registry-mirrors": ["http://127.0.0.1:65001"]
    ```

3. Restart dockerd.

    ```bash
    systemctl restart docker
    ```

4. Download an image with Dragonfly.

    ```bash
    docker pull nginx:latest
    ```

## Related Topics

- [Installing Server](userguide/install_server.md)
- [Installing Client](userguide/install_client.md)
- [Downloading Files](userguide/download_files.md)
- [SuperNode Configuration](userguide/supernode_configuration.md)
- [Dfget](cli_ref/dfget.md)
