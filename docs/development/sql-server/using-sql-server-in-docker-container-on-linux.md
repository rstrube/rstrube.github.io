---
title: 'Using SQL Server in a Docker Container on Linux'
tags: ['development','sql server','docker','linux']
date: 2023-01-04
---
# Using SQL Server in a Docker Container on Linux {: .primaryHeading }
<small>Last updated: 2023-01-04</small>
{: .primaryDate }

---

## Installation and Configuration
- [x] Download latest Docker image for SQL Server 2022
```shell
docker pull mcr.microsoft.com/mssql/server:2022-latest
```

- [x] Start a new container instance from the downloaded Docker image
```shell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" -p 1433:1433 --name sql1 --hostname sql1 -v sql1:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2022-latest
```

!!! note

    In addition to creating a new Docker instance, we are also creating a volume by using the `-v` switch. This will create a volume that maps container storage to your host storage.  In this case the volume is called `sql1`.

    You can name the image whatever you want, for this example we will use an image name of `sql1`.

- [x] Confirm that the new instance is running
```shell
docker ps -a
```

- [x] Confirm the location of the volume (this will tell you your path on your host machine)
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

- [x] Copy MDF and LDF files to the container (this will indirectly copy them to the volume on your host machine):
```shell
# copy the mdf and ldf files
docker cp ./myFiles/MyProject.mdf sql1:/var/opt/mssql/data/
docker cp ./myFiles/MyProject_log.ldf sql1:/var/opt/mssql/data/

# update permissions for mdf and ldf files, sql server can only work with files with mssql:root permissions
docker exec -it --user root sql1 chown mssql:root /var/opt/mssql/data/MyProject.mdf
docker exec -it --user root sql1 chown mssql:root /var/opt/mssql/data/MyProject_log.ldf
```

- [x] Create new databases using SQL

!!! note

    To do this you'll need to connect to your database using vscode (with SQL Server extension) or Azure Data Studio.  Once connected you should be able to run SQL.

!!! example

    Creating a new database using the previously copied mdf and ldf files:
   
    ```sql
    CREATE DATABASE MyProject
    ON
    (filename = N'/var/opt/mssql/data/MyProject.mdf')
    LOG ON
    (filename = N'/var/opt/mssql/data/WyProject_log.ldf')
    FOR ATTACH;
    ```

    Creating a new database using only an mdf file (assuming there is no accompanying ldf file):

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