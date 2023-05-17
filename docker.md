# Docker Notes

###### tags: `programming`

[toc]

# Basics
## Containerize an app
### DockerFile
### `docker build`
at the directory of Dockerfile, run 
```shell
docker build -t [name_of_image] .
```
* `-t [name_of_image]`

### `docker run`
at the directory of Dockerfile, run 
```shell
$ docker run \
    -dp [local_port]:[container_port] \
    [name_of_image]
```
* `-d`: runs in detached mode (background)
* `-p`: create port mapping for accessing the app

### See running containers
`$ docker ps`

### See available images
`$ docker image ls`

### See container logs
`$ docker logs -f <container-id>`

### Access runnung container
`$ docker exec [container_id] [shell comand]`

## Update the app + re-deployment
1. run `docker ps` to get the running `[container_id]`
2. run `docker stop [container_id]` to stop the container
3. run `docker rm [container_id]` to delete the image
4. build and deploy the app again

## Push image to Dokcer Hub
1. make sure to have an account on [Docker Hub](https://hub.docker.com/)
2. create a repo having same name as the local image name
3. **tag** the local image with username on Docker Hub
    `docker tag [image_name] [user_name]/[image_name]`
3. push the image to Docker hub
    `docker push [user_name]/[image_name]`
    
if one wants to run the container on different machine, just run:
```shell
$ docker run \
    -dp [local_port]:[container_port] \
    [user_name]/[image_name]
```

## Volume Mount
to persist data between container and local machine, use volume

### Create a volume
```shell!
$ docker volume create [volume_name]
```

### Attach(Mount) a volume to container
use `--mount type=volume,src=[volume_name],target=[path_to_save_data]`
```shell!
$ docker run \
    --mount type=volume,src=[volume_name],target=[path_to_save_data] \
    -dp [local_port]:[container_port] \
    [image_name]
```
in this way, docker will persist any data created under directory `[path_to_save_data]`

> EX: for example, if the app is storing data in a file located at `/etc/todos/todo.db` **in the container's filesystem**
> we could use a volume to persist `/etc/todos/todo.db`
> ```shell
> ### create a volume named "todo-db"
> $ docker volume create todo-db 
> 
> ### mount the volume to the container
> $ docker run \
>     --mount type=volume,src=todo-db,target=/etc/todos \
>     -dp 3000:3000 \
>     image_name
> ```

> **More Details**
> when using volume, docker stores the data on the disk, and we can see the data location by running
> ```shell
> $ docker volume inspect [volume_name]
> ```
> ![](https://hackmd.io/_uploads/rk76AClrn.png =400x)
>
> we could see the location in `Mountpoint` field

## Bind Mount
use bind mount to share a directory between host and container.
> useful when the app is in development since container could see and respond to the changes made to the code, etc. without rebuilding the image

## Comparison between Volume and Bind
|  | Volume | Bind |
| -------- | -------- | -------- |
| location | docker chooses | user chooses |
| `--mount` | `type=volume,src=[volume_name],target=[local_path]` | `type=bind,src=[local_path],target=[container_path]` |
| Populates new volume with container contents | Yes | No |
| Supports Volume Drivers | Yes | No |

> EX: running app in development container
> in the directory of the `app`, run:
> ```shell
> $ docker run -dp 3000:3000 \
>     -w /app --mount type=bind,src=$(pwd),target=/app \
>     node:18-alpine \
>     sh -c "yarn install && yarn run dev"
> ```
> * `-w`: specifying working directory (same as `WORKDIR` in Dockerfile)
> * `--mount`: bind current dir (`app`) to the container's `/app`
> * `node:18-alpine`: image to use (same as Dockerfile)
> * `sh`: (alpine doesn't have `bash`) install dependencies via `yarn install`, and start development server via `yarn run dev` (in `package.json` may be set as `nodemon [...]` for watching filesystem changes)
> 
> now, the container responds to changes made to code in /app
>![](https://hackmd.io/_uploads/BJvefZGS3.png =400x)

## Container Network
It is good practice to assign one job to one container at a time.
For containers to communicate, we use container network.

![](https://hackmd.io/_uploads/HyDsgfGr2.png =400x)

### Create a Network
```shell!
$ docker network create [network_name]
```

### Attach a Container to a Network
```shell=
$ docker run \
    --network [network_name] \
    --network-alias [container_ip_alias] \
    [...rest_of_command]
```
* `--network`: docker network to attach to
* `--network-alias`: docker would resolve this alias to an ip
    
> after container A is attached to a network, we can let another container B communicate with A by specifying environment variable `[container_A_ip_alias]` as host ip when running `docker run`

> EX: communication between SQL and APP
> 1. first create network
>     `$ docker network create app_network`
> 2. attach SQL container to the created network
>     ```shell
>     $ docker run -d \
>         --network app_network \
>         --network-alias mysql \
>         -v todo-mysql-data:/var/lib/mysql \
>         -e MYSQL_ROOT_PASSWORD=secret \
>         -e MYSQL_DATABASE=todos \
>         mysql:8.0
>     ```
>     * `-v`: a simpler way to mount a volume, `[volume_name]:[path_to_save_data]`
>     * `-e`: set environment variable
>     
>     now, the SQL host ip alias is `mysql`
> 3. start the APP container that communicates with SQL container via host ip alias `mysql`
>     ```shell
>     $ docker run -dp 3000:3000 \
>           -w /app -v "$(pwd):/app" \
>           --network todo-app \
>           -e MYSQL_HOST=mysql \
>           -e MYSQL_USER=root \
>           -e MYSQL_PASSWORD=secret \
>           -e MYSQL_DB=todos \
>           node:18-alpine \
>           sh -c "yarn install && yarn run dev"
>     ```
>     we can see that the env variable `MYSQL_HOST` is set to **`mysql` which docker would resolve to the actual host ip of the SQL container**

### See available container network
`$ docker network ls`

### Delete Network
`$ docker network rm [network_name]`

## Docker Compose