# docker-s3-minio

This repository is a template to implement S3 container using minio/minio image.
A bucket named 'somebucketname' will be create and files can be found in storage folder.

How to launch the service
```docker-compose up -d```

How to access the service: you can use the following parameters.
- Hostname: localhost
- Port: 9000
- User: minio
- Password: minio123

How to stop the service
```docker-compose down```

### Infos

You will have 4 containers :
- minio : the s3 storage,
- s3_nginx : a nginx server to proxy minio,
- minioclient : a container to create a bucket, copy files from local to s3 storage,
- awscli : a container to use aws cli to list files in s3 storage for example.

The 2 last containers are not mandatory. They will be still running after the launch command.
So you can use them to test your s3 storage.

For minioclient:
```
$ docker exec -ti minioclient /bin/sh
```

For awscli:
```
$ docker exec -ti awscli /bin/sh
```

Here is an example of how to create a configuration file for aws cli.
```
$ docker exec -ti awscli /bin/sh
sh-4.2# aws configure
AWS Access Key ID [None]: minio
AWS Secret Access Key [None]: minio123
Default region name [None]:
Default output format [None]: json
```

NB: Files are copied from local/s3/import directory to bucket `somebucketname` while starting the container.

```
sh-4.2# aws --endpoint-url=http://minio:9000 s3 ls s3://somebucketname
PRE rep1/
PRE rep2/
```

```
sh-4.2# aws --endpoint-url=http://minio:9000 s3 ls s3://somebucketname --recursive
2023-10-27 23:58:35          5 rep1/file1.txt
2023-10-27 23:58:35          6 rep2/files2.txt
2023-10-27 23:58:35          6 rep2/files3.txt
```

### Where to find minio administration : http://localhost:9000

### Logs

```
$ docker-compose up
Creating network "myproject_network" with the default driver
Creating minio ... done
Creating s3_nginx ... done
Creating minioclient ... done
Creating awscli      ... done
Attaching to minio, s3_nginx, awscli, minioclient
minio          | MinIO Object Storage Server
minio          | Copyright: 2015-2023 MinIO, Inc.
minio          | License: GNU AGPLv3 <https://www.gnu.org/licenses/agpl-3.0.html>
minio          | Version: RELEASE.2023-10-25T06-33-25Z (go1.21.3 linux/amd64)
minio          | 
minio          | Status:         1 Online, 0 Offline. 
minio          | S3-API: http://172.24.0.2:9000  http://127.0.0.1:9000 
minio          | Console: http://172.24.0.2:9001 http://127.0.0.1:9001 
minio          | 
minio          | Documentation: https://min.io/docs/minio/linux/index.html
minio          | Warning: The standard parity is set to 0. This can lead to data loss.
s3_nginx       | /docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
s3_nginx       | /docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
s3_nginx       | /docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
s3_nginx       | 10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
s3_nginx       | 10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
s3_nginx       | /docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
s3_nginx       | /docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
s3_nginx       | /docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
s3_nginx       | /docker-entrypoint.sh: Configuration complete; ready for start up
minioclient    | Removed `local` successfully.
minioclient    | Added `local` successfully.
minioclient    | Removed `local/somebucketname/` successfully.
minioclient    | Bucket created successfully `local/somebucketname/`.
minioclient    | Access permission for `local/somebucketname` is set to `public`
minioclient    | `/import/rep1/file1.txt` -> `local/somebucketname/rep1/file1.txt`
minioclient    | `/import/rep2/files3.txt` -> `local/somebucketname/rep2/files3.txt`
minioclient    | `/import/rep2/files2.txt` -> `local/somebucketname/rep2/files2.txt`
minioclient    | Total: 17 B, Transferred: 17 B, Speed: 171 B/s
```

Sources:
- https://min.io/docs/minio/container/index.html
- https://docs.min.io/docs/minio-docker-quickstart-guide.html
- https://docs.aws.amazon.com/cli/latest/reference/s3/ls.html
- https://github.com/minio/minio/issues/4769  NB: 'mc policy' is replaced by 'mc anonymous'