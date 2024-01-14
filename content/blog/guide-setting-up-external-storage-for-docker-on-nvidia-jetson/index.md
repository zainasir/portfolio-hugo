---
title: "Guide: Setting Up External Storage for Docker on Nvidia Jetson"
date: 2024-01-05T23:02:02-05:00
description: "This is a step-by-step guide on how to set up external storage on Nvidia Jetson AGX Xavier and configuring docker with it."
tags: ["tutorial", "docker", "nvidia jetson", "agx xavier", "external storage"]
---
While working on my research project, I ran out of space on my Nvidia Jetson AGX Xavier due to docker containers and limited space on the Jetson. With no choice but to use external storage, I spent almost an entire day trying to figure out how to mount an SD Card and configure Docker to use that storage. This blog post if for those in a similar situation.

## Preliminary Setup
My development environment is based on the following:
- Nvidia Jetson AGX Xavier
- Docker 24.0.7
- Ubuntu 20.04
- SD Card formatted as ext4

> Make sure that your external storage is formatted as ext4. This avoids any permission issues that Docker might face when writing to your external storage after mounting it.

## Step-by-step
- Mounting External Storage
    1. After inserting your SD Card into the Jetson module, there are multiple ways to figure out its relative path. In Jetson AGX Xavier, it is usually at `/dev/mmcblk1p1`. You can also use the following command `sudo fdisk -l` to list all the available partitions.
        ```sh
        sudo fdisk -l
        ```
    {{< figure src="assets/fdisk.png" attr="Output of *fdisk* command showing the SD Card mounted at */dev/mmcblk1p1*." align=center >}}

    2. We need to mount this external storage by appending the following line to the end of `/etc/fstab`.
        ```text
        /dev/mmcblk1p1  /mmcblk1p1  ext4    defaults,nofail     0   2
        ```

        This creates a folder at */mmcblk1p1* where the SD Card */dev/mmcblk1p1* gets mounted. The flags *defaults,nofail* make sure that this is a non-blocking mount point to avoid Jetson from getting stuck at boot if the SD Card is not inserted.

        {{< figure src="assets/fstab.png" attr="Mount point for sd card added at the end of *fstab*." align=center >}}

    3. Reboot to apply changes. You might notice that the SD Card is not accessible through the regular file viewer, which is completely fine.

- Configure Docker
    1. Stop docker services:
        ```sh
        sudo systemctl stop docker.service
        sudo systemctl stop docker.socket
        ```
    2. Open the docker config file with your favourite text editor:
        ```sh
        sudo emacs /lib/systemd/system/docker.service
        ```
    3. Find the line that starts with *ExecStart* and modify it to include the *--data-root* flag:
        ```text
        ExecStart=/usr/bin/dockerd --data-root /mmcblk1p1/docker_data -H fd:// --containerd=/run/containerd/containerd.sock
        ```
        Note that I added a separate folder *docker_data* at the end of our mount point */mmcblk1p1* to keep the files organized.

        {{< figure src="assets/dockerservice.png" attr="SD Card path added to the *ExecStart* variable as the *--data-root*." align=center >}}

    4. Transfer old docker files (only required if you want to keep your old containers and data):
        ```sh
        sudo rsync -aqxP /var/lib/docker /mmcblk1p1/docker_data
        ```
    5. Reload docker daemon and restart services:
        ```sh
        sudo systemctl daemon-reload
        sudo systemctl start docker
        ```
    6. Confirm docker data root is correctly configured:
        ```sh
        sudo docker info | grep "Root Dir"
        ```
        The above command should output something like this:
        {{< figure src="assets/dockergrep.png" attr="Docker info showing data root dir correctly updated." align=center >}}

## Things to note
Configuring external storage with docker should be done carefully because docker utilizes a lot of read/writes for logs which leads to wear out for certain storage types such as SD Cards and flash drives. It is better to use external hard drives such as a SSD for that purpose.
In case that using an SD Card is absolutely required, I recommend adding the following config options to optimize docker for less disk writes.
- Add the following config options to `/etc/docker/daemon.json`
    ```json
    {
        "log-driver": "local",
        "log-opts": {
            "max-size": "10m"
        }
    }
    ```
