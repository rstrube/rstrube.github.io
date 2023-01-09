---
title: 'SQL Server in a Docker Container on Linux'
tags: ['development','sql server','docker','linux']
date: 2023-01-09
---
# SQL Server in a Docker Container on Linux {: .primaryHeading }
<small>Last updated: 2023-01-09</small>
{: .primaryDate }

---

## Installation and Configuration
- [x] Download latest Docker image for SQL Server 2022:
```shell
docker pull mcr.microsoft.com/mssql/server:2022-latest
```

- [x] Start a new container instance from the downloaded Docker image:
```shell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" -p 1433:1433 --name sql1 --hostname sql1 -v sql1:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2022-latest
```

!!! note

    In addition to creating a new Docker instance, we are also creating a volume by using the `-v` switch. This will create a volume that maps container storage to your host storage.  In this case the volume is called `sql1`.

    You can name the image whatever you want, for this example we will use an image name of `sql1`.

- [x] Confirm that the new instance is running:
```shell
docker ps -a
```

- [x] Confirm the location of the volume (this will tell you your path on your host machine):
```shell
docker volume inspect sql1
[
    {
        "CreatedAt": "2022-12-19T16:17:37-07:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/sql1/_data",
        "Name": "sql1",
        "Options": null,
        "Scope": "local"
    }
]
```

- [x] Copy MDF and LDF files to the container (this will indirectly copy the files to the volume on your host machine):
```shell
# copy the mdf and ldf files
docker cp ./myFiles/MyProject.mdf sql1:/var/opt/mssql/data/
docker cp ./myFiles/MyProject_log.ldf sql1:/var/opt/mssql/data/
```

- [x] Update the permissions of the MDF and LDF files within the container:
```shell
# update permissions for mdf and ldf files, sql server requires mssql:root permissions on the files
docker exec -it --user root sql1 chown mssql:root /var/opt/mssql/data/MyProject.mdf
docker exec -it --user root sql1 chown mssql:root /var/opt/mssql/data/MyProject_log.ldf
```

- [x] Create new databases using SQL

!!! tip

    To do this you'll need to connect to your database using [Visual Studio Code](https://aur.archlinux.org/packages/visual-studio-code-bin) (with [SQL Server extension](https://marketplace.visualstudio.com/items?itemName=ms-mssql.mssql)) or [Azure Data Studio](https://aur.archlinux.org/packages/azuredatastudio-bin).  Once connected you should be able to run the SQL required to create new DBs inside the container.

!!! note

    To login, use the following connection parameters:

    * Server = `localhost`
    * Authentication type = `SQL Login`
    * User name = `SA`
    * Password = `<password entered when creating Docker container>`
    * Encryption = `False`
  
!!! note

    As long as you're mapping to the default port (`1433`), you should not have to provide it.

!!! example "Creating a new database using the MDF and LDF files copied into the container"

    ```sql
    CREATE DATABASE MyProject
    ON
    (filename = N'/var/opt/mssql/data/MyProject.mdf')
    LOG ON
    (filename = N'/var/opt/mssql/data/WyProject_log.ldf')
    FOR ATTACH;
    ```

!!! example "Creating a new database using only an MDF file (i.e. there is no accompanying ldf file)"

    ```sql
    CREATE DATABASE MyProject
    ON
    (filename = N'/var/opt/mssql/data/MyProject.mdf')
    FOR ATTACH;
    ```

## Starting a Shell in the Docker Container
If you would like to start a shell within the Docker container you can use the `docker exec` command:

```shell
# start a shell in the docker image
sudo docker exec -it sql1 bash

# start a shell (as root) in the docker image
sudo docker exec -it --user root sql1 bash
```