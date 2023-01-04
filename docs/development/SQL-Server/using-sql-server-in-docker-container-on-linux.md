---
title: 'Using SQL Server in a Docker Container on Linux'
tags: ['development','sql server','docker','linux']
---
# Using SQL Server in a Docker Container on Linux
1. Download latest Docker image for SQL Server 2022

```shell
docker pull mcr.microsoft.com/mssql/server:2022-latest
```

2. Start a new instance of the Docker image

*Note: we are also creating a volume here: (`-v`) this will create a volume that maps container storage to your host storage.  In this case the volume is called `sql1`.* 

```shell
docker run -e "ACCEPT_EULA=Y" -e "MSSQL_SA_PASSWORD=<YourStrong@Passw0rd>" -p 1433:1433 --name sql1 --hostname sql1 -v sql1:/var/opt/mssql -d mcr.microsoft.com/mssql/server:2022-latest
```

*Note: you can name the image whatever you want, for this example we will use an image name of `sql1`*

3.  Confirm that the new instance is running

```shell
docker ps -a
```

4. Confirm the location of the volume (this will tell you your path on your host machine):

```shell
docker volume inspect sql1
```

5. Copy MDF and LDF files to the container (this will indirectly copy them to the volume on your host machine):

```shell
docker cp ./myFiles/MyProject.mdf sql1:/var/opt/mssql/data/
docker cp ./myFiles/MyProject_log.ldf sql1:/var/opt/mssql/data/

# Update permissions for MDF/LDF files, SQL server can only work with files with mssql:root permissions
docker exec -it --user root sql1 chown mssql:root /var/opt/mssql/data/MyProject.mdf
docker exec -it --user root sql1 chown mssql:root /var/opt/mssql/data/MyProject_log.ldf
```

5. Create new databases using SQL

   To do this you'll need to connect to your database using VSCode (with SQL Server extension) or Azure Data Studio.  From there you can run SQL.  Here is an example of how to create a new database using an existing MDF and LDF file:
   
```sql
CREATE DATABASE MyProject
ON
  (filename = N'/var/opt/mssql/data/MyProject.mdf')
LOG ON
  (filename = N'/var/opt/mssql/data/WyProject_log.ldf')
FOR ATTACH;
```

If you only have an MDF file, you can skip attaching the LDF file:

```sql
CREATE DATABASE MyProject
ON
  (filename = N'/var/opt/mssql/data/MyProject.mdf')
FOR ATTACH;
```

Additional common tasks:

```shell
# Start a shell in Docker image
sudo docker exec -it sql1 bash

# Start a shell (as root) in the Docker image
sudo docker exec -it --user root sql1 bash
```

